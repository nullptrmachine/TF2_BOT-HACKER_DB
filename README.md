
# *TF2* BOT and Hacker DATABASE

*Done with perfectly trained ML model that was trained on large amount of specific demos including hackers and BOTs.
After all that there's detection algorithm created by model.*

**I'm not gonna open source it because Valve gon' instantly copypaste however, you can access this list**

>All is within >**80%** confidence | **includes OMEGATRONIC, DoesHotter and other BOTS, HACKERS etc.**

## Implementation
**steamID2** is format used in this list equal to **friendsID** in source engine games
[U:1:**X**] < The **X** is **friendsID**

**Example how you can obtain this list in simple HTTP request and load data into memory**
```c++
#include <iostream>
#include <string>
#include <vector>
#include <wininet.h>

#pragma comment(lib, "wininet.lib")

std::string get_url_data(std::string host, std::string url)
{
	std::string request_data = "";

	HINTERNET hIntSession = InternetOpenA("", INTERNET_OPEN_TYPE_DIRECT, NULL, NULL, 0);

	if ( !hIntSession )
		return request_data;

	HINTERNET hHttpSession = InternetConnectA(hIntSession, host.c_str(), 80, 0, 0, INTERNET_SERVICE_HTTP, 0, NULL);

	if ( !hHttpSession )
		return request_data;

	HINTERNET hHttpRequest = HttpOpenRequestA(hHttpSession, "GET", url.c_str(), 0, 0, 0, INTERNET_FLAG_RELOAD, 0);

	if ( !hHttpSession )
		return request_data;

	const char* szHeaders = "Content-Type: text/html\r\nUser-Agent: License";

	char szRequest[1024] = { 0 };

	if ( !HttpSendRequestA(hHttpRequest, szHeaders, strlen(szHeaders), szRequest, strlen(szRequest)) )
		return request_data;

	CHAR szBuffer[1024] = { 0 };
	DWORD dwRead = 0;

	while ( InternetReadFile(hHttpRequest, szBuffer, sizeof(szBuffer) - 1, &dwRead) && dwRead )
	{
		request_data.append(szBuffer, dwRead);
	}

	InternetCloseHandle(hHttpRequest);
	InternetCloseHandle(hHttpSession);
	InternetCloseHandle(hIntSession);

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
    std::vector<unsigned> vecSteamID;
    
    std::string data = get_url_data(
        "raw.githubusercontent.com",
        "/nullptrmachine/TF2_BOT-HACKER_DB/main/database"
    );
    
    if ( data.empty() )
        return EXIT_FAILURE;
    
    std::vector<std::string> vecData = split_string(data, "\n");
    
    for ( auto& str : vecData )
        vecSteamID.push_back(std::stoi(str));

    return EXIT_SUCCESS;
}
```
