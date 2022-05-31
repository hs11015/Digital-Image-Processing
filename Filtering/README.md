# Digital-Image-Processing - skin detection

1. Implement Smoothing Filter

![image](https://user-images.githubusercontent.com/79328858/171149225-13725bcf-3c88-47e7-84fa-31a427b9f80d.png)

input으로 Mask의 크기를 입력받아서 작성했다. 밑에서 서술할테지만 Median 필터의 경우 필터의 모양이 여러 가지가 될 수 있기 때문에 height와 wide의 길이를 각각 입력받았다.

  1) Mean Filter HSI 사용하지 않고 바로 Channel 값에 넣기 

![image](https://user-images.githubusercontent.com/79328858/171149241-7f756d32-3847-4da7-8225-977a32b8b6e9.png)

original image의 height와 width를 각각 높이와 너비로 하는 결과 값을 넣을 plt와, maskheight, maskwidth를 각각 높이와 너비로 하는 mean을 선언한다.
mean의 모든 픽셀 개수는 maskheight*maskwidth 개이다. 
모든 셀은 1/(전체 픽셀 개수) 의 값을 가지고, 모든 셀 값의 합은 1이 되도록 한다.
mask가 한 칸씩 움직이면서 plt에 mean과 src 값을 곱한 값을 각각 넣어준다. 단, mask가 maskheight, maskwidth의 값보다 작은 위치에는 올라갈 수 없기 때문에
if i<maskheight or i>height-maskheight or j<maskwidth or j>width-maskwidth :
문을 넣어서 위아래로 maskheight 길이보다 짧은 곳, 좌우로 maskwidth보다 짧은 곳에는 mean filter를 적용하지 않고, 원래 값을 취하는 조건을 넣었다.

  2) Gaussian Filter HSI 사용하지 않고 바로 Channel 값에 넣기 

![image](https://user-images.githubusercontent.com/79328858/171149261-020942a6-f184-4942-a8e9-17a563a91e7c.png)

가우시안 필터는 sigma를 필요로 한다. sigma가 1보다 커질수록 분포가 높아진다.

  ![image](https://user-images.githubusercontent.com/79328858/171149285-aec82d4d-25b0-488b-bfa8-ec36ab452f6b.png)

시그마 값은 사용자가 임의로 입력할 수 있도록 input 함수로 넣어두었다. (단, 정수여야함)
gaussian filter는 위 사진에 나오는 식대로 (1/(2*pi*sigma*sigma)) * e ** (-((i**2) + (j**2))/(sigma*sigma))를 사용해 거리 별로 gaussian filter 값을 채워주고, gaussian 값의 총 합을 g_sum이라는 정수형 변수에 담는다. 이후 mean filter와 같은 맥락으로 maskheight, maskwidth 보다 짧은 곳은 src의 값 그대로를 결과값으로 갖게 하고, mask가 올라갈 수 있는 곳에는 temp 를 이용해 gaussian필터*원본 / (gaussian필터 내 전체 합)을 해준 후 plt에 넣는다. 

  3) Median Filter

  ![image](https://user-images.githubusercontent.com/79328858/171149310-4e05d44f-0b50-4a1f-bb4c-431bea58eab7.png)

  
중앙값 필터는 필터의 모양이 여러 개가 있기 때문에 코드가 조금 길다.
1: 가로(너비만큼만),
2: 세로(높이만큼만),
3: 십자가모양(너비=높이이고 둘 다 홀수일때 가능),
4: 사각형(너비*높이 형태),
5: X자형태(너비=높이이고 둘 다 홀수일때 가능),
6: 다이아몬드모양(너비=높이이고 둘 다 홀수일때 가능)
위 6개 모양의 필터를 모두 구현하려다 보니 함수가 길어졌다.
사용자가 원하는 모양의 필터를 고르면 그 모양의 Median Filter로 결과를 출력해준다.

  ![image](https://user-images.githubusercontent.com/79328858/171149333-5dc4c1de-b380-40e6-88b1-836710148b37.png)

  
① ㅡ자

  ![image](https://user-images.githubusercontent.com/79328858/171149363-99e1e105-ed2a-4639-8992-9c0b6b7df276.png)

horizontal 모양의 Mask일 때는 origin 좌표가 딱 가운데 지점이 된다.
median filter의 경우 퀵소팅을 통해 크기순으로 정렬을 한 후 중앙값을 찾아야하기 때문에 B, G, R을 각각 나눠서 구했다. 마스크를 옮겨가면서 원본 사진인 src에서 B값, G값, R값을 각각 취한 후 퀵소팅으로 정렬 후 maskwith//2로 중앙값을 plt(결과값)에 나타냈다.
퀵소팅은 시간복잡도가 O(NlogN)으로 효율이 굉장히 좋은 정렬방법이다.

② ㅣ자

  ![image](https://user-images.githubusercontent.com/79328858/171149392-28af80ef-8d89-40a9-9c2c-4578864686f4.png)

Vertical은 horizontal와는 정 반대로 origin 좌표가 높이의 가운데 지점에 있다.
하지만 filter를 투과하여 B값, G값, R값 각각 리스트에 저장하고, 퀵소팅으로 정렬하여 중앙값을 찾아내 결과를 추출한다는 점에서는 같다.

③ 십자가 모양

  ![image](https://user-images.githubusercontent.com/79328858/171149405-e2efef00-70c4-47e1-8573-ac6a03ed3a31.png)

가로, 세로만 있는 것이 아니라 이제는 가로와 세로가 합쳐진 + 모양이다.
이 경우 origin이 가로와 세로가 만나는 교차점에 위치하기 때문에 maskheight와 maskwitdh가 같은 정사각형 모양이어야한다. + 모양이므로 중간 행, 중간 열 값들만 취해준다. 따라서 중간행이 아닐 때 중간 열이 아니라면 값을 취할 필요가 없어 continue 해줬고, 중간 행이거나 중간 열에 위치하면 원본 사진의 B값, G값, R값들을 각각 취해 horizontal, vertical filter와 마찬가지로 리스트에 넣고 퀵소팅해 중앙값을 찾아 결과 값으로 추출해준다.

④ square 사각형

  ![image](https://user-images.githubusercontent.com/79328858/171149419-e2ee8379-30ff-429d-bcde-f109ee8966d2.png)

사각형 형태의 mask 모양은 홀수*홀수 형태로, origin 값이 정 중앙에 위치하도록 한다.
다른 mask들과 마찬가지로 순서대로 B, G, R값을 취해 리스트에 넣고 퀵소팅해 중앙값을 결과값으로 추출해준다.

⑤ X자

  ![image](https://user-images.githubusercontent.com/79328858/171149459-a828bdd6-ef6b-4180-8ac0-3d9778f8a1c2.png)

이 경우 origin이 왼쪽 대각선과 오른쪽 대각선의 교차점에 위치하기 때문에 maskheight와 maskwitdh가 같은 정사각형 모양이어야한다. 
if n == m or n == maskwidth-1 - m 문을 이용하여 중간 열을 기점으로 반을 접었을 때 데칼코마니가 되는 부분을 행에 따라 하나씩 늘려가며 B, G, R 값을 추출했다.
그 다음에는 역시 퀵소팅 후 결과값으로 중앙값을 추출하는 순서이다.

⑥ 마름모, 다이아몬드 형태

![image](https://user-images.githubusercontent.com/79328858/171149479-01253432-897d-405d-973f-bba1764d1361.png)

이 모양을 구현하는 것이 가장 어려웠다. X자와 마찬가지로 열의 중간지점을 기준으로 행이 늘어날 수록 해당하는 cell도 두 칸씩 늘리고, 행의 중간지점을 지나면 다시 두 칸씩 줄이는 방법으로 구현했다. 이역시 RGB값을 받고 퀵소팅해 중앙값을 결과값으로 출력한다.

①~⑥까지 모두 plt이라는 결과 값 채널에 넣었으므로 plt를 return 해준다.

  4) main cell

![image](https://user-images.githubusercontent.com/79328858/171149487-4a364902-15e0-455b-aee2-e032775379ad.png)

위에서 정의해놓은 함수들을 이용할 수 있는 main cell이다.
mean, gaussian, median filter를 적용한 이미지들을 각각 볼 수 있게 만들어두었다.

![image](https://user-images.githubusercontent.com/79328858/171149500-d5e30c3f-ff3b-4beb-a934-4c78c625fd5f.png)

2. Eliminate some noises using either Gaussian filter and Median filter and Compare their performance

1.에서 구현한 Mean, Gaussian, Median Filter를 이용해 이미지를 출력 후 비교분석해보자.

<결과 1>
  
  ![image](https://user-images.githubusercontent.com/79328858/171149529-5c250141-8e28-4303-a0d6-9ae7fe3b1477.png)

gaussian noise 이미지를 사용하여 진행해보았다. 3*3 마스크로 진행했고, 가우시안 필터의 경우 시그마 값을 3으로 하여 진행했다. median 필터의 경우 6가지의 다른 모양의 mask를 모두 적용한 결과 값이다. 이미지가 작아서 결과 값이 빠르게 나왔고, 그에 비해 결과가 확연히 구분되지 않았다. 원본과 비교해보았을 때 mean, gaussian, median filter 모두 노이즈가 조금 흐려진 것을 확인할 수 있었다.

  ![image](https://user-images.githubusercontent.com/79328858/171149557-34cc9a33-8721-4de8-98c0-c9bb5c6126ad.png)

  
<결과 2>

  ![image](https://user-images.githubusercontent.com/79328858/171149588-bc8726e3-c5db-4d91-bce9-a07da2b89bc5.png)

마스크의 크기를 제외한 모든 조건은 <결과 1>과 동일하게 했다.
마스크의 크기만을 5*5로 바꾼 것뿐인데 노이즈가 확연히 사라져보였다. Mean filter가 가장 흐리게 보이고, Median filter의 경우 1번째는 가로 1행으로 이뤄진 마스크, 2번째는 세로 1열로 이루어진 마스크라 그런가 1번쨰는 노이즈가 가로로 길게 흐려진 양상, 2번쨰는 노이즈가 세로로 길게 흐려진 양상을 띠고 있다. Gaussian Filter의 경우 다른 필터들과 비교했을 때 노이즈도 많이 제거됨과 동시에 윤곽선은 살아있다.

![image](https://user-images.githubusercontent.com/79328858/171149604-3eb3171c-b978-4e83-b01f-810e9b797e74.png)

  
Fig0504(a)(gaussian-noise).jpg

  ![image](https://user-images.githubusercontent.com/79328858/171149631-0a1a3d60-dcb6-472c-8dce-ed632cd9a1c1.png)

Fig0513(a)(ckt_gaussian_var_1000_mean_0).jpg 원본, mean, gaussian

  ![image](https://user-images.githubusercontent.com/79328858/171149653-3aace14b-b624-4360-8b65-14d683b83975.png)

Fig0513(a)(ckt_gaussian_var_1000_mean_0).jpg 위 이미지의 경우 크기가 커서 잘 보이지 않아 3*3은 서술하지 않았었는데, 5*5에서는 mean, gaussian filter의 경우 노이즈가 조금 사라지고 살짝 블러처리된 부분이 보인다.


Fig0513(a)(ckt_gaussian_var_1000_mean_0).jpg Median filter 1~6

Median filter의 경우 노이즈는 눈에 띄지 않고, mask 모양에 따라서 명도가 조금씩 달라지는 것 같아 보인다.

Fig0513(a)(ckt_gaussian_var_1000_mean_0).jpg 는 이미지에서 노이즈가 잘 보이지 않아 Gaussian 이미지는 이번 한 번만 쓰고 넘기겠다..!

<결과 3>

  ![image](https://user-images.githubusercontent.com/79328858/171149700-58b4069e-f2bd-4c13-95ef-c9d4b339d99a.png)

이번에도 역시 mask의 크기를 제외하고는 모든 조건을 동일하게 했다.
마스크의 크기가 커져서 for 반복문을 많이 돌아야하다보니 3*3 마스크보다는 확연히 실행 속도가 느려졌다. 하지만 동시에 노이즈 또한 많이 제거된 것을 한 눈에 볼 수 있었다. Meanfilter의 경우 노이즈 뿐만 아니라 다른 색의 경계선까지 흐려진 것을 볼 수 있었다. 반면 Gaussian Filter의 경우 노이즈는 제거되고, 윤곽선은 많이 흐려지지 않았다. Median Filter 중 5번째 (X자 모양) 필터는 대각선으로 줄이 가 있는 것과, 중간 회색 네모의 모서리가 살짝 둥글어진 것을 볼 수 있었는데, 이는 median filter가 X자 모양으로 생긴 데 많은 영향을 받은 것으로 보인다.

![image](https://user-images.githubusercontent.com/79328858/171149714-30b1accc-fa81-4d2d-a3ca-72714def36f3.png)


<결과 4>
가우시안 필터에서 모든 조건이 동일할 때 시그마 값에 따른 변화를 심층적으로 알아봤다.

  ![image](https://user-images.githubusercontent.com/79328858/171149745-3936b557-cbb8-4627-9e38-5def43c6b0d6.png)
![image](https://user-images.githubusercontent.com/79328858/171149764-d957c6cd-1aed-46b4-9e6f-3ac65d42d452.png)
![image](https://user-images.githubusercontent.com/79328858/171149771-586e286a-51ff-4833-83fb-a43b58ce42c1.png)

3*3: 시그마 값 = 1 /  시그마 값 = 2   /  시그마 값 = 3   /    시그마값 = 4
시그마 값이 커질 수록 노이즈가 많이 사라졌다.
3*3이라 잘 안 보이는 것 같아 7*7으로 실행해보았더니 확연한 차이를 볼 수 있었다.
  
![image](https://user-images.githubusercontent.com/79328858/171149803-5249a0e1-3989-4c3f-a2d3-cb0aeae85b13.png)
![image](https://user-images.githubusercontent.com/79328858/171149817-60415c77-c9cc-49b8-bf17-e9a649a7f42a.png)

시그마 = 2				시그마 = 5

이를 통해 가우시안 필터의 경우 시그마 값이 커지면 커질 수록 더 블러처리 된다는 것을 알게 되었다.

<결과 5>

![image](https://user-images.githubusercontent.com/79328858/171149849-36569b9f-39dd-49da-afd7-51e2da03c028.png)

![image](https://user-images.githubusercontent.com/79328858/171149859-9a3fc5ca-e224-4811-8dc0-7f4766593486.png)

이번에는 salt&pepper noise를 가지고 있는 이미지를 넣어보았다.
3*3 필터임에도 불구하고 결과가 확연히 눈에 보였다. Median Filter가 가장 깨끗하게 나왔고, Mean Filter와 Gaussian filter는 이미지가 조금 흐려질 뿐 노이즈는 사라지지 않았다. 노이즈가 가장 깔끔하게 제거된 것은 Median Filter 3(십자가 모양), 5(X자 모양), 6(다이아몬드 모양)이다.

  ![image](https://user-images.githubusercontent.com/79328858/171149868-ea82a13c-3dd5-4835-b979-62f02867ffa1.png)

Fig0504(i)(salt-pepper-noise).jpg

  ![image](https://user-images.githubusercontent.com/79328858/171149878-20175b9a-8c04-4d04-9950-3dda97ba360b.png)

Fig0510(a)(ckt-board-saltpep-prob.pt05).jpg 원본, median 2, median 3

  ![image](https://user-images.githubusercontent.com/79328858/171149887-ecddea1f-cccc-4cd2-b509-d1b0af3dc716.png)

Fig0510(a)(ckt-board-saltpep-prob.pt05).jpg median 1, mean, gaussian, madians 4,5,6

이 이미지 역시 3*3 filter로 추출한 결과이다. salt&pepper의 경우 mean filter에서가 gaussian filter에서보다 더 잘 제거가 되었다. 하지만 이 둘보단 Median filter에서 더 잘 제거가 된 것으로 보인다.

Median filter에서는 5번째의 Mask 모양인 X자 형태의 Median filter가 가장 노이즈를 잘 제거한 것으로 보여진다. 그 다음으로는 6번째 다이아몬드 형태의 Mask 모양을 가졌던 Median filter가 가장 노이즈를 잘 제거하였다.



<결과 6>
똑같은 조건에서 필터의 크기만 3*3에서 7*7으로 바꿔서 시도해보았다. 
확실히 Median filter의 경우 노이즈가 거의 다 제거되었다. 다만, 중앙 값을 찾아서 임의로 픽셀 값에 넣는 과정이기 때문에 horizontal 모양(Median Filter 1)이었던 것은 픽셀들이 전체적으로 가로로 밀려났고, vertical 모양(Median Filter 2)이었던 것을 픽셀들이 전체적으로 세로로 튀어나온 부분이 있다. Median Filter 중 가장 깔끔하게 노이즈가 제거된 것을 고르라면 노이즈가 제거됨과 동시에 회색 네모 박스의 모양에 전혀 흔들림이 없는 cross 십자가 모양 (Median Filter 3)을 고를 것이다. 중간 원의 경우 살짝 흔들린 부분이 보이지만, 아무래도 + 모양으로 되어있어서 위와 아래에서 오는 벡터 값, 왼쪽과 오른쪽에서 오는 벡터값이 상충되어 회색 부분 네모는 전혀 영향을 받지 않은 것으로 보인다.
반면, Median Filter 4(square 모양)의 경우 오히려 노이즈가 더 심해졌다. 아무래도 가장 많은 픽셀을 포함하는 Median filter였기 때문에 Mask 안에 있는 픽셀 중 노이즈가 많이 위치하여 노이즈의 색이 중앙값이 될 확률이 컸던 것 같다.
Mean Filter와 Gaussian Filter는 어떤가? 개인적으로 Salt & pepper 노이즈의 경우 Mean filter나 Gaussian Filter는 쓰지 않는 것이 좋을 것 같다. 이미지가 전체적으로 흐려져서 노이즈가 분산되어 보일 뿐이지 Median Filter처럼 노이즈가 생긴 부분을 직접적으로 채워주지는 않는다. 결국 노이즈는 전혀 사라지지 않았고, 흐려지므로써 경계선 또한 불명확해졌다.

  ![image](https://user-images.githubusercontent.com/79328858/171149918-aa9d6fae-bad7-4732-89a0-b41018184145.png)

  ![image](https://user-images.githubusercontent.com/79328858/171149931-8ef46610-8116-408f-8455-79192fc10ad9.png)

Fig0510(a)(ckt-board-saltpep-prob.pt05).jpg 원본, median 2, median 3

![image](https://user-images.githubusercontent.com/79328858/171149939-3415d12f-79cf-4582-9a03-22ece8466974.png)

Fig0510(a)(ckt-board-saltpep-prob.pt05).jpg median 1, mean, gaussian, madians 4,5,6

위에 서술했던 Fig0504(i)(salt-pepper-noise).jpg 사진과는 같이 지금 보여드린 Fig0510(a)(ckt-board-saltpep-prob.pt05).jpg 의 경우에도 mean filter의 경우 흐리게만 보여지고 제대로 noise가 제거되지 않았고, gaussian filter 역시 mean filter보다는 좀 윤곽선이 뚜렷해 블러 처리가 심하게 되지는 않았으나, 여전히 noise는 제거되지 않았다.
하지만 median filter의 경우 4번째 square 모양 mask만 빼고 모두 다 잘 제거된 것으로 보인다. 특히 육안으로 보았을 때는 median 3, 5, 6은 noise가 거의 보이지 않을 정도로 제거되어 깨끗한 이미지를 출력해냈다.


<결과 7>

아무런 노이즈도 없는 오리지널 이미지를 가지고 filter를 실행하면 어떻게 될까?
3*3 Mask에 가우시안 필터의 경우 시그마값을 3으로 한 결과이다.
Median 값들은 중앙 값이라 자기 자신을 중앙값으로 가질 확률이 아무래도 크기 때문에 변화가 전혀 없다. 따라서 이후 이 사진을 비교할 때는 Mean 값과 Gaussian 값만 비교하려고 한다.

![image](https://user-images.githubusercontent.com/79328858/171149956-c794bf97-e4f8-427c-a334-8dd5b33f95fc.png)
