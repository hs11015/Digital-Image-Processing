# Digital-Image-Processing - skin detection

1. Prewitt, Sobel과 같은 1차 미분 연산자

Prewitt filter를 썼을 때
Px filter convolution, Py filter convolution, 두 값을 더한 값


Sobel filter를 썼을 때
Sx filter convolution, Sy filter convolution, 두 값을 더한 값

※ 큰 차이가 안 나는 것처럼 보이지만 sobel filter가 prewitt filter보다 조금 더 많은 edge를 검출할 수 있음. X filter와 Y filter는 엣지가 검출되는 방향이 다르다는 것을 알 수 있음. 그리고 X filter와 Y filter를 각각 convolution 한 후에 X filter convolution 한 이미지를 제곱한 값 더하기 Y filter convolution 한 이미지를 제곱한 값에 루트를 씌운 것이 맨 오른쪽 이미지가 된다.

Prewitt filter를 썼을 때
Px filter convolution, Py filter convolution, 두 값을 더한 값


Sobel filter를 썼을 때
Sx filter convolution, Sy filter convolution, 두 값을 더한 값

※ 여기서는 위의 결과보다 조금 더 잘 보이는데 prewitt filter보다 sobel filter가 더 많은 엣지를 검출하고 있다는 것을 알 수 있음. 오른쪽 앞에 있는 파프리카에서 특히 흰 부분이 더 많이 보인다. 또한, 전체적으로 흰 부분이 많이 검출되므로 엣지가 크게 검출된다고 판단할 수 있다.

Prewitt filter를 통과시켰을 때와 Threshold까지 시켰을 때
128 이상일 경우 흰색으로, 128 미만일 경우 검정색으로 표시하였다. 그 결과 엣지가 아님에도 희끗희끗하게 표현되던 부분들은 검정색으로 변하고, 정말 edge인 부분들만 흰색으로 표현되는 것을 확인할 수 있었다.


Prewitt filter부터 Threshold 시킨 값까지


Sobel filter부터 Threshold 시킨 값까지

※ Threshold 한 값을 보면 sobel filter를 convolution 시킨 값이 더 세세한 edge까지 검출되게 만든다는 것이 더 확연히 보인다. 하지만 너무 세세한 부분까지 edge 검출을 하기 떄문에 prewitt filter가 더 좋게 느껴질 수도 있다.


Prewitt filter부터 Threshold 시킨 값까지


Sobel filter부터 Threshold 시킨 값까지

※ 이 사진에서는 더 잘 관측할 수 있는데, Threshold를 하지 않은 값을 보아도 수염 부분을 보면 sobel filter를 convolution 시킨 값이 더 세세한 edge까지 검출되게 만든다는 것을 알 수 있다.


Prewitt filter부터 Threshold 시킨 값까지


Sobel filter부터 Threshold 시킨 값까지

※ 큰 차이 없지만 lena 사진에서도 sobel filter를 이용한 엣지 검출이 이목구비와 모자의 깃털 부분 edge를 더 많이, 세세하게 검출할 수 있다는 게 보였다.


Prewitt filter부터 Threshold 시킨 값까지


Sobel filter부터 Threshold 시킨 값까지

※ Prewitt filter와 Sobel filter 모두 X filter의 경우에는 세로 edge가 잘 검출되었고, Y filter의 경우에는 가로 edge가 잘 검출되었다. 따라서 각각의 image를 보면 겉에 네모 edge가 한 방향으로만 검출된 것을 확인할 수 있다. 하지만 이를 합쳐서 prewitt filter, sobel filter로 나타내어 보니 겉부분의 edge가 모두 제대로 잘 검출되었다는 것을 알 수 있다. 또한, Prewitt filter의 경우 겉의 네모 edge가 너무 얇게 검출되어 threshold 시킨 image에는 제대로 검출되지 않은 것을 볼 수 있었다. 반면 Soble filter의 경우 겉의 네모가 진하게 검출되어서 Threshold image에서도 검출된 것을 확인할 수 있었다.

아래 사진들 (boats.bmp, butterfly.jpg) 모두 Prewitt filter, Prewitt Threshold, Sobel filter, Sobel Threshold 값들을 뽑아낸 것이다. Prewitt filter와 Sobel filter 자체의 이미지만으로도 충분히 차이가 잘 느껴지긴 하지만, threshold 시킨 값들로 보면 sobel filter에서 확실히 edge가 더 잘 검출되었다는 것을 알 수 있다.

Prewitt filter, Threshold 시킨 값

Sobel filter, Threshold 시킨 값

Prewitt filter, Threshold 시킨 값


Sobel filter, Threshold 시킨 값

특히 butterfly.jpg 사진에서 Sobel filter의 경우 호랑나비의 무늬와 주위 꽃들의 edge를 제대로 잘 검출해낸 것을 확인할 수 있었다. Prewitt filter의 경우 호랑나비의 일부 무늬와 주의 꽃들의 edge가 제대로 검출되지 않았거나 흐리게 검출되었다는 것과 비교해보면 확연히 차이가 난다는 것을 알 수 있다.

Prewitt filter 코드
Px filter와 Py filter를 각각 선언하고 filtering 함
Px의 경우 [[-1 0 1], [-1 0 1], [-1 0 1]]
Py의 경우 [[1 1 1], [0 0 0], [-1 –1 -1]]











Px filter와 Py filter를 각각 선언하고 filter 값들을 모두 convolution 시킴
Px의 경우 [[-1 0 1], [-1 0 1], [-1 0 1]]
Py의 경우 [[1 1 1], [0 0 0], [-1 –1 -1]]
Px와 Py 모두 convolution 하고 Px로 나온 값 제곱, Py로 나온 값 제곱 후 루트를 씌운 것















Sobel filter 코드
Sx filter와 Sy filter를 각각 선언하고 filtering 함
Sx의 경우 [[-1 0 1], [-2 0 2], [-1 0 1]]
Sy의 경우 [[1 2 1], [0 0 0], [-1 –2 -1]]







Sobel filter 코드
Sx filter와 Sy filter를 각각 선언하고 filtering 함
Sx의 경우 [[-1 0 1], [-2 0 2], [-1 0 1]]
Sy의 경우 [[1 2 1], [0 0 0], [-1 –2 -1]]
Sx와 Sy 모두 convolution 하고 Sx로 나온 값 제곱, Sy로 나온 값 제곱 후 루트를 씌운 것











main code이다. threshold의 경우 함수를 썼다.
binary로 나타내는데, 127 이하일 경우에는 0(검정)으로, 128이상일 경우에는 1(흰)으로 나타내어 보았다.






2. LoG를 이용하여 에지를 검출




가우시안 필터에서 시그마 값을 너무 높이면 엣지가 제대로 검출되지 않는다는 것을 알았다. 아무래도 필터의 차이 값(음수와 양수의 차이)가 너무 작아져서 그런 거라 생각한다.





이 경우 오히려 시그마 값이 높은 게 엣지 검출이 더 잘 됐다. 필터의 크기가 커서 필터의 차이 값(음수와 양수의 차이)가 작더라도 주위에 비교하는 픽셀들이 많아서 엣지가 더 잘 검출되었다고 생각한다.






사진에 비해 필터의 값이 너무 커질 경우 edge가 크고 겹겹이 검출된다는 것을 알았다.








LoG filter 코드이다.
filter size와 gaussian filter sigma값은 input으로 입력을 받아 filter에 적용하게끔 코드를 작성하였다. 두 번 미분한 가우시안 필터값이라 식은 교재에 나온대로 적용하였다.




3. OpenCV 라이브러리에 있는 Canny edge operator 코드 실행








Canny Edge의 경우 Minimal Threshold Value가 작고, Maximal Threshold Value가 클 수록 더 많고 세세한, 정확한 edge가 검출됨을 알 수 있다. 







edge가 단순하고 명확하게 나타난 image의 경우 세밀한 edge 값들이 존재하지 않기 때문에 Minimal Threshold Value와 Maximal Threshold Value에 큰 영향을 받지 않고 똑같은 edge를 검출해낸다는 것을 알았다.











컬러 사진을 Gray scale로 받고 Canny Edge 처리를 하는데, 이 경우 Minimal Threshold Value가 무조건 작고, Maximal Threshold Value가 무조건 클수록 정확한 edge가 검출되는 것은 아님을 알 수 있다. 두 Threshold 값의 차이가 클수록 더 많고 세세한 edge들이 검출됨을 알 수 있는데, 이게 정확한 edge가 아닐 확률이 크다.










