
# TF2 BOT & HACKER DATABASE

*NOTE: Anyone who tries to kick TFG user is automatically added to this database as well.*

**TFGuard is in development and will be released soon here on this account.**

**I'm not gonna open source it because Valve gon' instantly copypaste however, you can access this list**

>**includes OMEGATRONIC, DoesHotter and other BOTs, HACKERs etc.**

## Implementation
**steamID2** is format used in this list equal to **friendsID** in source engine games
[U:1:**X**] < The **X** is **friendsID**

**Example how you can obtain this list in simple HTTP request and load database into memory**
```c++
#include <iostream>
#include <string>
#include <vector>
#include <wininet.h>

#pragma comment(lib, "wininet.lib")

std::string make_http_request(const std::string& host, const std::string& url, const std::string& headers)
{
	std::string request_data;

	HINTERNET hInternetSession = InternetOpenA("", INTERNET_OPEN_TYPE_DIRECT, NULL, NULL, 0);
	if ( !hInternetSession )
		return request_data;

	HINTERNET hHttpSession = InternetConnectA(hInternetSession, host.c_str(), INTERNET_DEFAULT_HTTP_PORT, 0, 0, INTERNET_SERVICE_HTTP, 0, NULL);
	if ( !hHttpSession )
	{
		InternetCloseHandle(hInternetSession);
		return request_data;
	}

	HINTERNET hHttpRequest = HttpOpenRequestA(hHttpSession, "GET", url.c_str(), 0, 0, 0, INTERNET_FLAG_RELOAD, 0);
	if ( !hHttpRequest )
	{
		InternetCloseHandle(hHttpSession);
		InternetCloseHandle(hInternetSession);
		return request_data;
	}

	std::string strHeaders;
	if ( headers.empty() )
		strHeaders = "Accept: text/html\r\nUser-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:68.0) Gecko/20100101 Firefox/68.0\r\n";
	else
		strHeaders = headers;

	const char* szHeaders = strHeaders.c_str();
	const char* szRequest = NULL;
	DWORD dwRequestLen = 0;

	if ( !HttpSendRequestA(hHttpRequest, szHeaders, strlen(szHeaders), (void*) szRequest, dwRequestLen) )
	{
		InternetCloseHandle(hHttpRequest);
		InternetCloseHandle(hHttpSession);
		InternetCloseHandle(hInternetSession);
		return request_data;
	}

	DWORD dwBytesRead = 0;
	const DWORD dwBufferSize = 4096;
	char szBuffer[dwBufferSize];

	while ( InternetReadFile(hHttpRequest, szBuffer, dwBufferSize - 1, &dwBytesRead) && dwBytesRead > 0 )
	{
		szBuffer[dwBytesRead] = 0;
		request_data += szBuffer;
	}

	InternetCloseHandle(hHttpRequest);
	InternetCloseHandle(hHttpSession);
	InternetCloseHandle(hInternetSession);

	return request_data;
}

std::vector<std::string> split_string(const std::string& str, const std::string& delimiter)
{
	std::vector<std::string> strings;

	std::string::size_type pos = 0;
	std::string::size_type prev = 0;

	while ( ( pos = str.find(delimiter, prev) ) != std::string::npos )
	{
		strings.push_back(str.substr(prev, pos - prev + 1));
		prev = pos + 1;
	}

	strings.push_back(str.substr(prev));
	return strings;
}

int main()
{
    // your storage
    std::vector<unsigned> vecSteamID;
    
    // try get data
    std::string data = make_http_request(
        "raw.githubusercontent.com",
        "/nullptrmachine/TF2_BOT-HACKER_DB/main/database"
    );
    
    // check data
    if ( data.empty() )
        return EXIT_FAILURE;
    
    // fragment data
    std::vector<std::string> vecData = split_string(data, "\n");
    
    // parse and append data
    for ( auto& str : vecData )
        vecSteamID.push_back(std::stoi(str));

    return EXIT_SUCCESS;
}
```
