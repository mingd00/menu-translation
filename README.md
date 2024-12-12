<h1>📜 메뉴판 사진을 활용한 영어 메뉴 정보 제공 서비스</h1> 

<img src="https://github.com/user-attachments/assets/550133f0-ce0e-4724-8ff8-515e5c54fa76" alt="image" width="500" height="250"/>

<확정된 기능>

- 사용자가 메뉴 사진을 촬영하여 업로드
- 이미지 인식으로 메뉴 인식 후 영어로 메뉴 정보 제공(이름, 가격, 재료, 알러지 주의 음식, 매움 정도)

<추가로 고려해 볼 사항>

- 다국어 지원
- 한국어 발음 영어로 지원(데이터에 정보가 없으므로 따로 수집하거나 open ai 사용 고려)
    - open ai 사용 X 한국어 발음 영어 표기로 전환하는 git에 있는 기능 확보
    
    [GitHub - kawoou/jquery-korean-pron: 한국어 발음 변환 라이브러리](https://github.com/kawoou/jquery-korean-pron)
    
    [GitHub - muik/transliteration: English to Korean Transliteration (영어-한글 표기 변환기)](https://github.com/muik/transliteration)
    

---

### **입력, 출력 데이터**

- 입력

```
메뉴판 이미지
-> 이미지 테스트 후 최소 해상도에 해당하는 이미지만 인식하도록 설정

```

- 출력

```
~~

```

---

### **YOLO 학습을 위해 준비해야 할 데이터**

- labels에 들어갈 txt 파일 생성

```
class_id x_center y_center width height # 데이터 형태
```

- 각 클래스에 해당하는 메뉴 정보가 담긴 json 파일(DB를 사용할 것인지 고민 필요)

---

### **원본 데이터 형식과 YOLO가 요구하는 형식**

- 원본 데이터 형식

```
dataset/
├── train/
│   ├── images/            # 학습용 이미지
│   │   ├── image1.jpg
│   │   ├── image2.jpg
│   │   └── ...
│   ├── labels/            # 학습용 라벨
│   │   ├── image1.json
│   │   ├── image2.json
│   │   └── ...
├── val/
│   ├── images/            # 검증용 이미지
│   │   ├── image3.jpg
│   │   ├── image4.jpg
│   │   └── ...
│   ├── labels/            # 검증용 라벨
│   │   ├── image3.json
│   │   ├── image4.json
│   │   └── ...

```

- 요구 형식

```
├── data.yaml
├── images/
│   ├── train/            # 학습용 이미지
│   │   ├── image1.jpg
│   │   ├── image2.jpg
│   │   └── ...
│   ├── val/              # 검증용 이미지
│   │   ├── image3.jpg
│   │   ├── image4.jpg
│   │   └── ...
├── labels/
│   ├── train/            # 학습용 라벨
│   │   ├── image1.txt
│   │   ├── image2.txt
│   │   └── ...
│   ├── val/              # 검증용 라벨
│   │   ├── image3.txt
│   │   ├── image4.txt
│   │   └── ...
```

---

### **원본 데이터의 labels 폴더 -> json 파일에서 필요한 정보**

- 해당 코드는 원본 데이터의 json 파일 구조이며 필요한 정보만 남겨 놓은 것(불필요한 정보는 제거)

```json
{
  "meta": {
    "file_name": "JR_KF03_M0001_1579396_4.jpg", // 해당 jpg 파일
  },
"annotations": [
    {
      // 메뉴 정보(한국어, 영어)
      "menu_information": {

	// 메뉴 이름
        "ko": "김치카츠나베",
        "en.ROMAN": "kimchi katsu nabe", // 발음
        "en": "Kimchi Cutlet Hot Pot", // 영어 번역
        "ja": "キムチ入りカツ鍋", // 일본어 번역

	// 가격
        "price": "13900",
      },

      "sn": "JR_KF03_M0001_1579396_4-tf.jpg_1", // 마지막 1만 클래스 아이디로 사용
      // 이미지 위치 정보
      "ocr": {
        "x": 5.561128752170819,
        "y": 18.18856020463471,
        "width": 17.43374927319452,
        "height": 1.7585844137780087,
      }
    },
```

- json 파일의 메뉴 정보는 중복으로 나타날 수 있으므로 미리 새로운 json 파일에 클래스 정보를 선언한 후 메뉴 이름에 맞는 클래스를 할당해주는 작업이 필요하다.
```json
{
	"class": 1 // 클래스
	"ko": "김치카츠나베", // 메뉴 이름
	"en.ROMAN": "kimchi katsu nabe", // 발음
	"en": "Kimchi Cutlet Hot Pot", // 영어 번역
	"ja": "キムチ入りカツ鍋", // 일본어 번역
	"price": "13900" // 가격 
}, 
```
