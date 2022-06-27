
[디지털영상처리] final 과제 						202012468 김현서

I. Hand detection

![image](https://user-images.githubusercontent.com/79328858/175873228-6a496d25-d831-4d95-b01a-1644d3967797.png)

![image](https://user-images.githubusercontent.com/79328858/175873241-66163154-d27b-4bb7-be23-0fa81024bd6d.png)

![image](https://user-images.githubusercontent.com/79328858/175873251-23cae91b-fa11-498a-b943-3c24c83caa03.png)

![image](https://user-images.githubusercontent.com/79328858/175873259-3270f63e-e504-40e5-84a6-c86334c99350.png)

dilate를 통해 손톱 부분, 손 오른쪽 그림자진 부분에 생긴 구멍을 메웠고,
erode의 반복 횟수를 조절함으로써 손 주변에 보이는 background인 초록색 배경을 최소화시킬 수 있었다.

이미지가 너무 크기 때문에 resize를 통해 크기를 줄여줌.
interpolation은 INTER_AREA가 이미지 축소에 가장 적절하므로 위로 정함
hsv를 통한 skinmask와 motion detection을 통한 fgmask 마스크들을 둘 다 합쳐 필터로 쓴다
fgmask의 경우 cv2.createBackgroundSubtractorKNN을 통해 배경 추출.
fgmask에서는 손가락이 움직일 때 손등이 detection되지 않는다는 단점이 있어 손등을 채우기 위해 cv2.floodFill을 이용해 채워줬다. 이후 skinmask와 fgmask를 합쳐 필터로 만들고, 원본 이미지를 축소시킨 resize_frame에 파랑 초록 빨강 channel에 각각 통과시킨 후 merge로 합쳤다. 그 과정에서 canny edge를 빼주었는데, 이렇게 하면 손에 가까운 배경 부분이 많이 제거될 것 같아서 실행하였다.
해당 결과는 dilate, erode 이외에도 medianBlur 처리 등을 얼마나 했는지에 따라 달라진다. median Blur 처리의 경우 모든 행동을 마치고 손을 내릴 때 Salt-Pepper noise와 같이 산발적인 점이 나타나서 해당 노이즈들을 없애주기 위해 적용하였다. 그 결과 손을 내릴 때에 깔끔한 결과를 얻을 수 있었다. 




II. Car Detection

![image](https://user-images.githubusercontent.com/79328858/175873318-49b12149-4e12-40e8-8bd1-8bb6a2a2f7b3.png)


움직이는 물체만을 검출하였다. 차만 detection되면 좋았을 것 같은데 맨 왼쪽에 움직이는 사람까지 검출되었다.
해당 오검출을 없애기 위해 어떻게 해야 할 지 고민을 많이 해보았지만, 정확한 방법을 찾아내지 못했다.


교수님께서 제공해주신 이미지가 너무 작아 resize 함수를 통해 영상을 확대했다.
이후 cv2.createBackgroundSubtractorKNN을 이용해 motion detection을 했고, 이를 통해 움직이는 부분을 mask로 만들었다. 자동차가 움직이면서 생긴 그림자를 제거하기 위해 threshold를 사용하였고, 그림자까지 포함된 mask와 그림자는 포함되지 않은 mask를 따로 지정하여 cv2.bitwise_xor으로 차량의 그림자에 해당하는 부분을 제거한 후 합쳤다. 그 이후 floodFill과 dilate를 이용하여 자동차의 그림자와 함께 사라진 자동차의 창문 같은 사이에 뚫린 구멍을 메웠고, hand detection 한 것 과 비슷하게 B, G, R channel에 각각 mask를 씌워 merge해서 차량을 검출했다.
