...
title: 위성데이터 다운받기(windos & python & TROPOMI)
layout: posts
comments: true
...

# < GES DISC 사이트에서 위성 자료 다운 받기(Python) >

- GES DISC는 GODDARD EARTH SCIENCES DATA AND INFORMATION SERVICES CENTER의 줄임말로 말그대로 쉽게 데이터를 다운받게 해주는 사이트라고 생각하시면 됩니다.

### 0. 환경 세팅하기
 - 만약 Earthdata 아이디가 없으시다면 먼저 [GES DISC 로그인](https://urs.earthdata.nasa.gov/){:target="_blank"} 해당 사이트에 접속하셔서 'register'를 누르고 가입을 해주신 후 진행하시면 됩니다. 회원가입 과정은 어렵지 않아 생략하겠습니다.




 - [GES DISC 다운로드](https://disc.gsfc.nasa.gov/){:target="_blank"} 사이트에 접속하신 후 'tropomi no2'라고 치시면 'Sentinel-5P TROPOMI Tropospheric NO2 1-Orbit L2 5.5km x 3.5km (S5P_L2__NO2____HiR 1)'라고  상단에 검색되는 데이터셋이 있는데 클릭 후 오른쪽 하단에 있는 'Subset / Get Data' 버튼을 누르시고 아래 사진을 참고하셔서 위성 데이터의 URL이 담긴 텍스트 파일을 받아주시면 됩니다.

### [1]
![0](https://drive.google.com/uc?id=1GcaKMWHauce2wB74DYvsOSMF3HB1c92r)

### [2]
![0.1](https://drive.google.com/uc?id=11vBqaGIVXmKPA6AyvF1bUzwo6QDHm2Kb)

### [3]
![0](https://drive.google.com/uc?id=1CraGbWh4divRl08FBNNwVB_7vWbcCckv)

### [4]
- 한반도 지역의 그림만 뽑아볼 예정이라 아래와 같이 위경도를 지정했습니다.


![0](https://drive.google.com/uc?id=1NvAXwzYzzofMzFvN3IQiH_0JGc9K6tBd)

### [5]


- Download links list를 누르면 우리가 사용할 데이터 위성 데이터의 다운로드 url파일이 받아지게 되며 이 파일은 원하시는 작업 디렉토리로 옮기시면 됩니다.


![0](https://drive.google.com/uc?id=1IX_JEeu1-H4r91R3lb9olVnh-glXEsQ9)

### [6]


- 마지막으로 아래와 같이 [내컴퓨터 -> C드라이브 -> 사용자 -> 본인 사용자 이름] 폴더에 들어가신 후 '.netrc' 라는 파일을 만들고 텍스트 편집기로 실행 후 'machine urs.earthdata.nasa.gov login 아이디 password 비밀번호' 를 입력해주시면 환경세팅은 완료가 됩니다.


![0](https://drive.google.com/uc?id=1PWQrMrVDpTYm5fp6rturYPeXYziwqWvI)

### 1. 필요한 모듈 불러오기
#### 1) requests 
 - 웹상에서 요청을 보낼수 있게 해주는 모듈로 일반적으로 크롤링이라는 작업을 할때 웹상의 데이터를 가져올 수 있게 해주며 여기서는 GES DISC로 요청을 보내 위성 데이터를 받을 수 있게 해주는 역할을 합니다. <br><br>
 
#### 2) glob
  - 간단하게 원하는 파일의 이름과 경로를 쉽게 불러올 수 있게 해주는 모듈로 예를 들어 '경로 + *.확장자명' 라고 입력하면 지정해준 경로의 모든 확장자명 파일의 경로 및 이름을 불러옵니다.
  
#### 3) os
  - 운영체제에서 제공하는 기능을 파이썬에서 수행할수 있게 해주는 모듈입니다.


```python
import requests
import glob
import os
```

### 2. url 텍스트 파일 불러오기
- 현재 저는 코드를 짜고 있는 폴더 내의 url_txt폴더에 제가 다운받을 url 텍스트 파일을 저장했습니다.

- 현재 디렉토리의 모든 폴더 및 파일을 불러옵니다
- dataset에 데이터를 저장할 예정이고, url_folder 에는 다운받을 url을 저장한 텍스트 파일이 있습니다.


```python
os.listdir()
```




    ['.ipynb_checkpoints', 'dataset', 'download_code.ipynb', 'url_folder']



- 아래와 같이 ./ 을 사용하여 현재 폴더 경로를 생략할 수 있습니다.
- ./url_folder/*.txt -> 현재 폴더중 url_folder 에서 확장자명이 텍스트인 모든 파일을 불러와 url_file이란 변수 이름으로 리스트 형식으로 저장


```python
url_file = glob.glob("./url_folder/*.txt")
print(url_file[0])
```

    ./url_folder\subset_S5P_L2__NO2____HiR_1_20210617_033504.txt
    

### 3. 파일 이름 정하기
- 조금 귀찮을 수도 있는 작업으로 url에서 파일이름을 고르는 작업입니다.
- 여기서는 split이란 함수(특정 문자열 기준으로 쪼개주는 함수)를 사용하여 파일 이름을 골랐습니다.
- '/' 기준으로 쪼갠후 리스트의 가장 마지막 문자열이 이름으로 적합해 보입니다.
- 다른 종류의 위성데이터를 사용하실때에는 아래와는 다른 기준으로 이름을 정하셔야 할거라 직접 여러방법으로 split 등을 하며 찾아주셔야합니다.


```python
f = open(url_file[0],'r')
lines = f.readlines()
i = 0
for line in lines[:5]:
    print('"/" 기준으로 쪼갰을떄의 리스트 \n', line.split('/'))
    print("\n위에서 나누었던 리스트 중 가장 마지막 문자열 : {}\n\n".format(line.split('/')[-1]))
```

    "/" 기준으로 쪼갰을떄의 리스트 
     ['https:', '', 'sentinel.esa.int', 'documents', '247904', '2474726', 'Sentinel-5P-Level-2-Product-User-Manual-Nitrogen-Dioxide\n']
    
    위에서 나누었던 리스트 중 가장 마지막 문자열 : Sentinel-5P-Level-2-Product-User-Manual-Nitrogen-Dioxide
    
    
    
    "/" 기준으로 쪼갰을떄의 리스트 
     ['https:', '', 'sentinel.esa.int', 'documents', '247904', '2476257', 'Sentinel-5P-TROPOMI-ATBD-NO2-data-products\n']
    
    위에서 나누었던 리스트 중 가장 마지막 문자열 : Sentinel-5P-TROPOMI-ATBD-NO2-data-products
    
    
    
    "/" 기준으로 쪼갰을떄의 리스트 
     ['https:', '', 'sentinel.esa.int', 'documents', '247904', '3541451', 'Sentinel-5P-Nitrogen-Dioxide-Level-2-Product-Readme-File\n']
    
    위에서 나누었던 리스트 중 가장 마지막 문자열 : Sentinel-5P-Nitrogen-Dioxide-Level-2-Product-Readme-File
    
    
    
    "/" 기준으로 쪼갰을떄의 리스트 
     ['https:', '', 'tropomi.gesdisc.eosdis.nasa.gov', 'data', 'S5P_TROPOMI_Level2', 'S5P_L2__NO2____HiR.1', '2019', '218', 'S5P_OFFL_L2__NO2____20190806T022006_20190806T040136_09388_01_010302_20190812T040854.nc\n']
    
    위에서 나누었던 리스트 중 가장 마지막 문자열 : S5P_OFFL_L2__NO2____20190806T022006_20190806T040136_09388_01_010302_20190812T040854.nc
    
    
    
    "/" 기준으로 쪼갰을떄의 리스트 
     ['https:', '', 'tropomi.gesdisc.eosdis.nasa.gov', 'data', 'S5P_TROPOMI_Level2', 'S5P_L2__NO2____HiR.1', '2019', '218', 'S5P_OFFL_L2__NO2____20190806T040136_20190806T054305_09389_01_010302_20190812T060847.nc\n']
    
    위에서 나누었던 리스트 중 가장 마지막 문자열 : S5P_OFFL_L2__NO2____20190806T040136_20190806T054305_09389_01_010302_20190812T060847.nc
    
    
    
    

### 4. 데이터 다운로드 하기
- 위에서 정했던 이름으로 여러데이터를 다운 받기 위해 코드를 작성해보겠습니다.
- 코드에 대한 내용은 아래 주석을 참고해주시면 됩니다.


```python
# 저장할 폴더 경로를 save_path 변수로 지정합니다.
save_path = './dataset/'
```


```python
f = open(url_file[0],'r')
lines = f.readlines()
i = 0

# 시간 관계상 텍스트 파일의 5번째 줄 파일까지만 받겠습니다.
# 텍스트 파일내 모든 파일을 받으시려면 그냥 [:] 로 두시면 됩니다.
for line in lines[:5]:
    
    
    
    # URL에서 공백이 있을 경우 다운로드가 되지 않아 공백을 제거하기 위해 strip 함수를 사용합니다
    URL = line.strip()
    FILE_NAME = (line.split('/')[-1]).strip()
    
    # nc확장자가 파일이름에 포함되어 있지 않다면 보통 메뉴얼 파일로 pdf 확장자명을 따로 붙여줘야 합니다.
    if 'nc' not in FILE_NAME:
        FILE_NAME = FILE_NAME + '.pdf'
    print('Start to Download ', FILE_NAME)
    
    result = requests.get(URL.strip())
    
    # 아래는 GES DISC 사이트에서 제공하는 파이썬 기준 다운로드 코드입니다.
    try:
        result.raise_for_status()
        f = open(save_path + FILE_NAME,'wb')
        f.write(result.content)
        f.close()
        print('contents of URL written to '+ save_path + FILE_NAME)
    except:
        print('requests.get() returned an error code '+ str(result.status_code))
    
    # 데이터를 얼마나 다운받았는지 확인하기 위해 임의로 작성해본 코드입니다.
    print((((i+1)/len(lines))*100),"% 진행\n")
    i += 1
    
print("모든 데이터 다운로드가 완료되었습니다.")
```

    Start to Download  Sentinel-5P-Level-2-Product-User-Manual-Nitrogen-Dioxide.pdf
    contents of URL written to ./dataset/Sentinel-5P-Level-2-Product-User-Manual-Nitrogen-Dioxide.pdf
    7.6923076923076925 % 진행
    
    Start to Download  Sentinel-5P-TROPOMI-ATBD-NO2-data-products.pdf
    contents of URL written to ./dataset/Sentinel-5P-TROPOMI-ATBD-NO2-data-products.pdf
    15.384615384615385 % 진행
    
    Start to Download  Sentinel-5P-Nitrogen-Dioxide-Level-2-Product-Readme-File.pdf
    contents of URL written to ./dataset/Sentinel-5P-Nitrogen-Dioxide-Level-2-Product-Readme-File.pdf
    23.076923076923077 % 진행
    
    Start to Download  S5P_OFFL_L2__NO2____20190806T022006_20190806T040136_09388_01_010302_20190812T040854.nc
    contents of URL written to ./dataset/S5P_OFFL_L2__NO2____20190806T022006_20190806T040136_09388_01_010302_20190812T040854.nc
    30.76923076923077 % 진행
    
    Start to Download  S5P_OFFL_L2__NO2____20190806T040136_20190806T054305_09389_01_010302_20190812T060847.nc
    contents of URL written to ./dataset/S5P_OFFL_L2__NO2____20190806T040136_20190806T054305_09389_01_010302_20190812T060847.nc
    38.46153846153847 % 진행
    
    
