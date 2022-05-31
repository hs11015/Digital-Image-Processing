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

  
  <결과 8>
크기를 조금 늘려서 3*3에서 7*7으로 늘려봤다. Mean값이 Gaussian값보다 훨씬 블러처리가 많이 된 것을 알 수 있다. 

    
사진 예시 : Fig0503 (original_pattern).jpg



(추가 사진 예시 Fig0507(a)(ckt-board-orig).jpg)

보다 확연하게 보기 위해 15*15의 크기를 갖는 filter를 만들어보았다.


사진 예시 : Fig0503 (original_pattern).jpg


(추가 사진 예시 Fig0507(a)(ckt-board-orig).jpg)


확실히 Mean filter가 gaussian filter보다 블러처리에는 효과가 있는 것 같다.
하지만 경계선을 최대한 덜 흐리게 하고, 블러처리를 하기 위해서는 Gaussian filter를 이용하는 것이 좋다는 것도 이번 결과를 통해 알 수 있었다.

<결론>
noise가 없는 original 사진에서는 Median Filter는 original 값 그대로를 출력하고, Gaussian filter는 적당히 블러된 값을, Mean Filter는 완전히 블러 처리된 값을 출력한다. 이 때 mask의 크기가 커지면 커질 수록 결과값의 강도도 세지게 된다.

Gaussian noise의 경우 gaussian filter 또는 median filter가 가장 블러처리가 잘 된다고 볼 수 있다. gaussian filter는 시그마 값이 커질 수록 더 부드러운 값을 낼 수 있게 된다(브럴 처리의 강도가 세진다)

Salt&pepper noise의 경우 Median filter를 써야한다. 0과 1을 값으로 갖는 픽셀들이 산발적으로 위치하고 있기 때문에 이 공백을 중앙값으로 메우기 위해서는 median filter가 가장 좋은 기능을 한다.











1. Apply average filter to given color image

위에서도 한 번 언급했었지만 Median filter는 color image에 적용하는 경우가 드물다. 아무리 퀵소팅을 사용하더라도 중앙값을 한 픽셀마다 구하는 연산에 많은 시간이 걸리기 때문이다. 또한, Median filter의 경우 원본의 RGB 값을 각각 받아서 중앙값을 취해오는 것이기 때문에 동일한 픽셀에서 중앙값을 취해오지 않을 수도 있다.
예를 들어 설명을 해보자면 만약 mask가 3*3인 filter를 이용해 R, G, B 각각의 중앙값을 가져오려고 했을 때 아래 사진처럼 각각 다른 픽셀에서 값을 가져와 아예 다른 색의 픽셀 값을 구성하게 되게된다. 원래 이미지에서는 R:100, G:20, B:150이었던 픽셀이지만 필터를 통과하면서 [0][0]에 있던 R: 255, [1][1]에 있던 B: 150, [2][2]에 있던 G:140을 취해와아예 다른 색의 pixel을 구성하게 되고, 이런 결과값들이 모여 하나의 이미지를 구성하게 되면 원래 이미지와 비교해보았을 때 전혀 다른 이미지를 구성하게 될 수 있다.

위에 서술한 이유 때문에 원본과 이미지가 확연히 달라질 수도 있는 Meaidn filter는 제외하고, 3번 color image에서는 mean filter와 gaussian filterd의 결과값들만을 위주로 서술할 것이다.

1) HSI 사용하지 않고 바로 Channel 값에 넣기
  1.과 2.에서 썼던 코드와 동일한 코드. channel 값에 바로 적용시켜주는 경우이다.
color 이미지와 흑백 이미지는 모두 동일한 특성을 가지고 있을 것으로 예상한다.

시작 전, 교수님께서 제공해주신 Lena_noise.png와 Gaussian noise.png의 값이 바뀌었다는 글을 보고 임의로 그림 파일을 바꿔 진행했다.
i) 3*3, 가우시안 시그마 : 3 

Lena_noise.png
원본에 비해 노이즈가 크게 줄지는 않았다. mean과 gaussian 사이 큰 차이는 없다.

Gaussian noise.png
원본에 비해 mean filter와 gaussian filter 모두 노이즈가 줄었다. mean과 gaussian간의 차이는 크게 보이지 않는다.

Salt&pepper noise
mean과 gaussian 사이 큰 차이는 없이 원본과 달라진 것 거의 없이 그대로이다.



  ii) 5*5, 가우시안 시그마 : 3

Lena_noise.png
원본에 비해 노이즈가 크게 줄지는 않았지만 mean과 gaussian 모두 조금 부드러워졌다.

Gaussian noise.png
원본에 비해 mean filter와 gaussian filter 모두 노이즈가 줄었고, mean filter을 사용했을 때의 결과 값이 gaussian filter을 사용했을 때의 결과값보다 좀 더 흐리게 나온다.

Salt&pepper noise
실제 내 화면에 출력되는 사진으로 보면 mean filter의 경우 original 이미지보다 확실히 blur처리가 되고 있고, gaussian filter 역시 smoothing은 되고 있지만 mean filter가 gaussian filter보다는 좀 더 효과적이다. 하지만 보고서에  사진을 넣으니 사진이 작아져서 그대로인 것처럼 보인다...

  iii) 7*7, 가우시안 시그마 : 3

Lena_noise.png
mean과 gaussian 사이 큰 차이는 없지만 mean이 gaussian보다는 좀 더 noise가 적다.

Gaussian noise.png
mean filter의 경우 많이 흐려졌고, gaussian filter의 경우 노이즈도 많이 제거되고 사진이 부드러워졌다.

Salt&pepper noise
mean filter의 경우 확실히 original image보다 많이 부드러워지고 노이즈도 많이 걷혔다. gaussian의 경우에도 original image와 비교했을 때는 부드러워지고 노이즈도 많이 없어졌다. 하지만 mean filter와 비교해보았을 때 Gaussian filter는 아직 많이 sharp하다.


  iv) 5*5, 가우시안 시그마 변화 주기 : 순서대로 1, 3, 5

Lena_noise.png
큰 차이는 없지만, 시그마의 값이 커질수록 사진이 smoothing 되기 때문에 노이즈가 조금씩 줄어드는 것으로 보인다.

Gaussian noise.png
Gaussian filter의 경우 mask의 크기는 그대로여도 시그마의 값이 커질수록 노이즈가 더 줄어들고, 사진이 부드러워진다는 것을 알 수 있다.

Salt&pepper noise
보고서에 사진을 넣으니 크기가 줄어들어 노이즈가 커 보이지만, 확실히 시그마의 크기가 커지면 커질 수록 noise의 양상은 줄어드는 것을 확인할 수 있다.

2) HSI 사용해 I 값을 수정하고 RGB 값으로 변환해 값에 넣기
  1.과 2.에서 썼던 코드와는 달리 기존 원본에 있던 RGB 값을 HSI로 변환한 후 I 값을 수정하여 HSI를 다시 RGB 값으로 바꿔 결과 사진을 도출해내는 코드이다.

i) 3*3, 가우시안 시그마 : 3 

Lena_noise.png
원본 이미지와 Mean filter, Gaussian filter를 적용했을 때의 결과 값에 큰 차이가 없다.

Gaussian noise.png
Mean filter가 Gaussian filter보다 노이즈를 조금 더 감소시킨 것을 확인할 수 있다.

Salt&pepper noise
원본 이미지와 Mean filter, Gaussian filter를 적용했을 때의 결과 값에 큰 차이가 없다.

  ii) 5*5, 가우시안 시그마 : 3

Lena_noise.png
원본 이미지에 비해 Mean filter, Gaussian filter 모두 조금씩 smoothing 된 결과를 추출했음을 확인할 수 있다.

Gaussian noise.png
원본 이미지에 비해 Mean filter의 경우 많이 흐려졌다. 노이즈는 거의 제거된 것처럼 보이고, 모자와 배경 사이처럼 사진 내의 경계선(edge)이 점점 흐려지고 있다. Gaussian filter를 적용했을 때는 noise는 많이 사라진 것으로 보이고, 경계선 또한 mean filter와 비교했을 때 잘 보존되고 있음을 확인 할 수 있다.

Salt&pepper noise
큰 차이가 없어 보이지만 Mean filter의 경우 노이즈를 포함한 이미지가 모두 smoothing 되었음을 확인할 수 있다. Gaussian filter의 경우도 원본 이미지보다는 smoothing 되었다.
  iii) 7*7, 가우시안 시그마 : 3

Lena_noise.png
Mean filter와 Gaussian filter 모두 원본 이미지와 비교했을 때 smoothing 되었다. mean filter와 gaussian filter만 놓고 보면 mean filter가 조금 더 smoothing된 양상을 띠고 있다.

Gaussian noise.png
Mean filter의 경우 5*5에서보다 더 흐려졌다. 노이즈는 거의 제거되고 경계선(edge)이 완전 흐려져 초점이 맞지 않는 것처럼 보인다. Gaussian filter를 적용했을 때 noise는 거의 사라졌고, 경계선 또한 mean filter와 비교했을 때 잘 보존되고 있음을 확인 할 수 있다.

Salt&pepper noise
원본 사진이 워낙 크다보니 보고서에 사진을 첨부했을 때 큰 차이가 없어 보인다. 하지만 mean filter의 경우 원본 사진보다 많이 smoothing 되었고, gaussian filter 역시 mean filter보다는 아니지만 많이 smoothing 되었음을 알 수 있다.
  iv) 5*5, 가우시안 시그마 변화 주기 : 순서대로 1, 3, 5

Lena_noise.png -> 차이를 잘 보이게 하기 위해 원본 사진 사이즈를 줄였음

Gaussian noise.png

Salt&pepper noise -> 차이를 잘 보이게 하기 위해 원본 사진 사이즈를 줄였음

세 이미지에서 모두 나타나듯 가우시안 시그마를 높일수록 사진이 더 smoothing 됨을 알 수 있다. 노이즈도 옅어짐과 동시에 사진의 edge(경계선)도 흐려진다.





+) 위 i)~iii)까지 썼던 Lena_noise와 Salt&pepper noise의 차이가 보고서에서 잘 보이지 않아 원본 사진의 사이즈를 줄여 3*3, 7*7 mask만 한 번 돌려보았다. (이때, 가우시안 시그마 값은 3으로 고정)

3*3 : Lena_noise.png -> 차이를 잘 보이게 하기 위해 원본 사진 사이즈를 줄였음

7*7 : Lena_noise.png -> 차이를 잘 보이게 하기 위해 원본 사진 사이즈를 줄였음

원본 사이즈를 줄여서 보니(전체 픽셀의 개수가 적어짐) 확실히 사진을 추출해내는 시간도 줄어들었다. 사진 이야기로 돌아와서, 마스크의 사이즈를 크게 해서 적용할 수록 smoothing 되는 정도가 커진다는 것을 알 수 있었다. 또한, mean filter가 gaussian filter보다 더 많이 블러처리 되었음을 알 수 있었다.
노이즈를 제거함과 동시에 edge(경계선)도 보존을 하려면 gaussian filter를, 
단순히 noise만 제거하고 edge(경계선)의 보존 여부는 상관이 없이 사진을 블러처리하는 것이 목적이라면 mean filter를 쓰는 것이 낫다.


3*3 : Salt&pepper noise -> 차이를 잘 보이게 하기 위해 원본 사진 사이즈를 줄였음

7*7 : Salt&pepper noise -> 차이를 잘 보이게 하기 위해 원본 사진 사이즈를 줄였음

salt&pepper noise 역시 마스크의 사이즈를 크게 해서 적용할 수록 smoothing 되는 정도가 커진다는 것을 알 수 있었다. 또한, mean filter가 gaussian filter보다 더 많이 블러처리 되었음을 알 수 있었다.
하지만 sal&pepper noise의 경우 mean filter와 gaussian filter 두 개로는 noise를 조금 흐리게 할 뿐 완벽히 제거할 수 없다. edge(경계선)도 보존을 하면서 noise를 줄이고 싶다면 gaussian filter를, edge(경계선)의 보존 여부는 상관이 없이 noise를 많이 흐리게 하는 것이 목적이라면 mean filter를 쓰는 것이 낫다.


2. Apply the High-boost filter to some image

첫 시작은 library를 불러오고 mask의 높이와 너비, A 값을 입력 받는 것으로 한다.


high-boost filtering 중 ① 상하좌우에만 mask를 적용시키는 함수를 mask_highpass1,
② 상하좌우 대각선 모두에 mask를 적용시키는 함수를 mask_highpass2로 선언했다.
  







① mask_highpass_1

우선 이렇게 마스크의 크기가 3*3→5*5→7*7→... 으로 커질 수록 다이아몬드 형태로 마스크를 적용하는 filter를 만들기로 했다.



highpass를 적용시키기 위해 원본 이미지의 BGR을 HSI로 변환하는 과정을 거쳤다.
Intensity에만 mask를 적용시킨 후 다시 BGR 값으로 변환하면 sharpening된 결과 이미지를 추출할 수 있다는 생각에 시작했다.


우선 위에서 설명한대로 다이아몬드 형태로 mask filter를 만든다.



그 후 if i<maskheight or i>height-maskheight or j<maskwidth or j>width-maskwidth
문을 넣어서 위아래로 maskheight 길이보다 짧은 곳, 좌우로 maskwidth보다 짧은 곳에는 mask를 적용하지 않고, 원래 값을 취하고 mask를 움직이도록 설정했다.
Hue 값의 범위에 따라 BGR 값을 구하는 식이 달라지기 때문에 H[i][j]의 범위에 따라 식을 수정하였다


다음 코드는 mask가 적용되는 곳의 값이다. hsi2B, hsi2G, hsi2R에 각각 원본 사진의 Indensity에 mask를 적용한 pixel 값들을 적용해 더해준다

모든 mask를 적용한 pixel들의 합이 plt[i][j](결과 이미지의 i+1행 j+1열 pixel 값)이 됨


② mask_highpass_2

우선 이렇게 마스크의 크기가 3*3→5*5→7*7→... 으로 커지면 mask의 모든 pixel만큼 square 형태로 마스크를 적용하는 filter를 만들기로 했다.



mask_highpass_1 함수와 다른 점은 mask 범위밖에 없다. 모든 픽셀을 mask에 적용시키기 위해 위처럼 mask를 만들어준다.(origin pixel 빼고 모두 –1)


이후 main cell

highpass1과 highpass2를 만들어서 각각 다른 mask를 적용시킨 결과값을 출력하도록 한다.






<결과 1> 원본 / origin pixel과 상하좌우만 mask / origin pixel과 상하좌우대각선 mask

mask의 크기는 3*3으로 동일하게 하고 A의 크기를 1, 1.2, 1.4로 늘려가며 결과를 비교하였다.

1) 3*3 mask / A = 1






2) 3*3 mask / A = 1.2










3) 3*3 mask / A = 1.4





A의 크기가 커질 수록 밝은 부분이 더 밝아져 더 선명하게 보인다.
mask들의 비교에서는 상하좌우만 mask 값을 적용한 것보다 상하좌우 대각선 모두 mask 값을 적용했을 때 더욱 sharpening 되었다는 걸 알 수 있다.
<결과 2> 원본 / origin pixel과 상하좌우만 mask / origin pixel과 상하좌우대각선 mask

mask의 크기는 5*5으로 동일하게 하고 A의 크기를 1, 1.2, 1.4로 늘려가며 결과를 비교하였다.

1) 5*5 mask / A = 1






2) 5*5 mask / A = 1.2











3) 5*5 mask / A = 1.4





A의 크기가 커질 수록 밝은 부분이 더 밝아져 더 선명하게 보인다는 것과
상하좌우만 mask 값을 적용한 것보다 상하좌우 대각선 모두 mask 값을 적용했을 때가 sharpening에 더 효과적이라는 것은 mask의 크기가 3*3일 때와 동일하다
또 마스크의 크기가 커질수록 sharpening이 더 많이 된다는 것 또한 알 수 있다.


위에서 내가 보인 것은 밝은 부분을 Sharpening 시킬 수 있는 High-boost를 구현한 거라High-boost = A*Original + Highpass이다.
여기서 Mask의 모양, filter만 바꾸면 어두운 부분을 Sharpening 시길 수 있는 High-boost 또한 구현이 가능하다. High-boost = A*Original – Lowpass이다.


이때 lowpass가 될 수 있는 값은 mean filter, gaussian filter 등이다.
  
  
