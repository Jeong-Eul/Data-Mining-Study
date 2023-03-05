# RSNA Screening Mammography Breast Cancer Detection

빅데이터 연합 동아리 BOAZ에서 4인팀으로 진행했던 미니프로젝트입니다.

● RSNA(Radiological Society of North America)는 전 세계의 방사선 전문가로 구성된 국제 학회이자 비영리 조직  
● 현재 유방암의 조기 발견을 위해서는 전문의의 통찰력과 견해가 필요하며, 이는 높은 비용과 시간을 수반 함  
● 따라서 RSNA에서는 Kaggle을 통해 치료의 품질과 안전을 개선하는 것을 목적으로 인공지능 경진대회를 주최하였음  

#### 제공된 Dataset
 - 여성 환자를 대상으로 수집한 유방 방사선 이미지가 포함됨
 - csv 파일에는 이미지의 방향, 환자의 나이, 조직검사 여부 등의 정형데이터가 함께 포함되어 있음
 
 ![image](https://user-images.githubusercontent.com/122766824/222948257-72d9581a-a917-475e-9d14-bd5567467a12.png)
 
 ## Efficient Net
 ![image](https://user-images.githubusercontent.com/122766824/222948383-beac399a-a202-4b5d-b5c3-c9c196fc07f2.png)
efficient net은 depth, channel width, resolution의 최적 조합을 찾아 이미지 분류 성능을 높인 모델이다.
본 프로젝트에서 본인은 이 모델로 실험을 진행했다.

## CLAHE
![image](https://user-images.githubusercontent.com/122766824/222948472-da7becf2-d118-4225-b05f-dad7c6ba5a50.png)

 - 타일 별로 이미지를 균일화 해 분포를 원만하게 변환시켜줌  
 - 유방암 조직의 경계를 더욱 선명하게 구분하고, 노이즈를 감소시키기 위해 사용  

![image](https://user-images.githubusercontent.com/122766824/222948496-fa89b911-c2ba-4c54-9d03-994c4b20ad1f.png)


## Grad-CAM
CNN 모델이 이미지의 어떤 부분에 집중하여 판단을 내렸는지 확인할 수 있음  
CNN의 마지막 feature map에 GAP를 적용하여 히트맵을 생성하여 구현됨

##### original image
![image](https://user-images.githubusercontent.com/122766824/222948551-92e2405b-c1b3-4340-888b-4e00f15cf5c3.png)

##### Grad-CAM image
![image](https://user-images.githubusercontent.com/122766824/222948554-ed6f37cc-9b60-4f72-b00c-3204415c6529.png)


