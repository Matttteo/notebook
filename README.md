# notebook
## libcurl获取response数据
自己写一个回调函数
```c++
size_t CurlWriteCallBackFunc(void* contents, size_t size, size_t nmemb, std::string* str) {
	size_t oldLen = str->size();
	size_t newLen = size * nmemb;

	try {
		str->resize(newLen + oldLen);
	}
	catch (std::bad_alloc& e) {
		std::cout << e.what() << std::endl;
		return 0;
	}
    std::copy((char *)contents, (char *)contents + newLen, str->begin() + oldLen);
	return newLen;
}

int PropQuery(std::string & ip, std::string & teamId, std::string * ret)
{
	if (ip.empty() || teamId.empty()) {
		return -1;
	}
	char buffer[100];
	sprintf_s(buffer, "http://%s:7802/prop?team_id=%s", ip.c_str(), teamId.c_str());
	CURL *pCurl = NULL;
	CURLcode res;

	pCurl = curl_easy_init();
	if (NULL != pCurl) {
		curl_easy_setopt(pCurl, CURLOPT_TIMEOUT, 1);

		curl_easy_setopt(pCurl, CURLOPT_URL, buffer);

		curl_slist *plist = curl_slist_append(NULL, "Content-Type:application/json;charset=UTF-8");
		curl_easy_setopt(pCurl, CURLOPT_HTTPHEADER, plist);
        curl_easy_setopt(pCurl, CURLOPT_WRITEFUNCTION, CurlWriteCallBackFunc);
        curl_easy_setopt(pCurl, CURLOPT_WRITEDATA, ret);

		res = curl_easy_perform(pCurl);

		if (res != CURLE_OK) {
			std::cout << "failed with " << curl_easy_strerror(res) << std::endl;
			return 0;
		}
		curl_easy_cleanup(pCurl);
	}
	return 0;
}

```

## C++ 随机数生成器
使用STL随机数生成器<random>，一个随机数生成器由两个部分组成：
1. 一个生产随机序列或伪随机序列的引擎（engine）
2. 一个将这些数值映射到一个随机分布的函数

一个简单的例子：
```c++
int norm(int mean, int sigma) {
    static std::default_random_engine re{};
    static std::normal_distribution<double> nd(mean, sigma);
    return round(nd(re));
}
int rand_int(int low, int high)
{
    static default_random_engine re{};
    using Dist = uniform_int_distribution<int>;
    static Dist uid{};
    return uid(re, Dist::param_type{ low,high });
}
```

## C++ 时间函数
使用STL<chrono>

一个简单的例子
```C++
    auto bg = std::chrono::steady_clock::now();
    // DO SOMETHING
    auto duration = std::chrono::steady_clock::now() - bg;
    cout << "time cost :" << std::chrono::duration_cast<std::chrono::milliseconds>(duration).count() << endl;
```
