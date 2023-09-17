# WebCrawler_HKelegislation_Case
# Installation

To run this project, make sure you have the following dependencies installed:

- Python 3.8
- requests 2.27.1
- beautifulsoup4  4.12.2
- jupyter_core  4.11.2
- selenium 4.12.0

# Introduction

**The crawled Url**: https://www.elegislation.gov.hk/index/chapternumber?TYPE=1&TYPE=2&TYPE=3&LANGUAGE=E&p0=1 

Our goal is to capture the content of each chapter of the legislation on the web page, summarize it into a CSV file, and download the different versions of the pdf and rtf to the corresponding path. The project uses two methods for document crawling, which are based on parsing source code and packet fetching and analyzing. Using the website of Hong Kong e-Legislation as an example, Requset, Beautifulsoup, and Selenium webdriver are used to crawl the relevant pdf and rtf documents. 

[![image](https://user-images.githubusercontent.com/71159153/268479853-43a5d9d2-9842-4217-ab2a-d0a1669eb5b1.png)](https://user-images.githubusercontent.com/71159153/268479853-43a5d9d2-9842-4217-ab2a-d0a1669eb5b1.png)

# Web scraping by parsing source code

***hkLegislation_src.ipynb*** contains two modules, data acquisition and document download.

The function of the data acquisition module: Analyze the contents of web pages and crawl relevant resources. Uses the Request library to access web pages. Uses Selenium to wait for the loading of dynamic data. Uses Beautiful Soup to parse HTML pages step by step to obtain the chapter name, file name, and download links of files in different languages of each chapter. Data is stored in the dataframe, the download path is manually set, and the dataframe is generated into a CSV file to facilitate data visualization and data analysis. The function of the document download module is: Using the Request library to request the corresponding download link of the dataframe, download it to the corresponding directory, and name it according to the specified format.

The crawled results are stored in the ***res.csv file***, with each column of the file corresponding to the value of each column of each chapter in the page. The structure of res.csv:

| Chapter / Instrument  No. | Chapter / Instrument Title | Bilingual_PDFurl | Bilingual_RTFurl | English_PDFurl | English_RTFurl | Chinese_PDFurl | Chinese_RTFurl | Filepath |
| ------------------------- | -------------------------- | ---------------- | ---------------- | -------------- | -------------- | -------------- | -------------- | :------: |
|                           |                            |                  |                  |                |                |                |                |          |

[![image](https://user-images.githubusercontent.com/71159153/268479795-e29d3a52-2a3e-4723-ac16-25f8bc3c63ea.png)](https://user-images.githubusercontent.com/71159153/268479795-e29d3a52-2a3e-4723-ac16-25f8bc3c63ea.png)

A total of **3081** chapters of legislation have been captured.

In the document download module,  to make the name of the downloaded file easier to organize, the following code attempts to get the Content-Disposition field in the response header to get the name that comes with the file.

```pytho
def get_filename(response):
    if response.status_code == 200:
        content_disposition = response.headers.get("Content-Disposition")
        if content_disposition:
            filename_match = re.search(r'filename\s*=\s*"([^;"]+)"', content_disposition)
            if filename_match:
                filename = filename_match.group(1).strip()
                return filename
            else:
                print("无法找到文件名")
        else:
            print("无法获取 Content-Disposition 字段")
```



# Web scraping by analysising data packet 

The first part of ***hkLegislation_package.ipynb***  is to get the json from the response. Because the information of the file is loaded dynamically, if selenium is not used to process the dynamically loaded data, another method is to capture the data packet and analyze it, obtain the returned json string, and extract the relevant information to get the download link.The json information of the downloaded file is stored in the ***package_json.txt*** file. The document is then downloaded using the information obtained in json.



