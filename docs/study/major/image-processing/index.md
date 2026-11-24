---
title: "영상처리"
---

# 영상처리

3학년 2학기에 수강한 영상처리 수업의 노트입니다. 영상의 표현 방식과 파일 포맷에서 시작해 Dithering, Point Processing, 공간 영역 필터링, Interpolation, Fourier Transform과 주파수 영역 필터링, 노이즈 제거, Segmentation, 컬러 영상 처리까지 MATLAB 예제와 함께 다룹니다.

- [What is Image Processing?](01-what-is-image-processing.md): 영상처리의 목적과 성능 평가, Sampling과 Nyquist 조건, Aliasing, Low Pass Filter, 영상 획득 과정
- [Basic Concepts of Image](02-basic-concepts-of-image.md): 영상처리의 응용 분야와 여러 측면, Binary·Gray Scale·True Color·Indexed Color 영상, Mach Bands, Gamma Correction
- [Color Types and Data Types of Image](03-color-types-and-data-types.md): MATLAB의 imread와 imshow, RGB 영상과 Indexed Color 영상, 자료형 변환, 파일 헤더와 Colormap, Vector와 Raster 영상, DPI
- [Image Types and Color](04-image-types-and-color.md): BMP 헤더 구조와 GIF·PNG·JPEG·TIFF 포맷, Pseudo Color, 가시광선 스펙트럼, Multispectral·Hyperspectral 영상
- [Basic of Image Display (Dithering)](05-image-display-dithering.md): imshow 함수, Bit Plane과 Watermark, 공간 해상도와 imresize, Quantization, Dithering, Error Diffusion, Half Tone
- [Point Processing](06-point-processing.md): 히스토그램과 PDF·CDF, 산술 연산과 보수 연산, Histogram Stretching과 imadjust, Histogram Equalization
- [Neighborhood Processing](07-neighborhood-processing.md): 2D Convolution과 Mask, LPF·HPF·Identity Kernel, filter2와 fspecial, Gaussian Filter, Unsharp Masking, ROI
- [Image Geometry](08-image-geometry.md): Nearest-neighbor·Linear·Cubic Interpolation, Spatial Filtering을 이용한 확대, 영상 축소와 회전
- [Fourier Transform](09-fourier-transform.md): 푸리에 급수, 1차원 DFT와 그 성질(Shifting, Scaling, Separability), DC 성분, 주파수 영역의 Ideal·Butterworth·Gaussian 필터링
- [Image Restoration](10-image-restoration.md): Gaussian·Salt-and-Pepper·Shot·Periodic Noise의 특성과 각 노이즈를 제거하는 방법
- [Image Segmentation](11-image-segmentation.md): Thresholding과 Double Thresholding, Otsu 방법, Adaptive Thresholding, 미분을 이용한 Edge 검출과 Prewitt Filter, 2차 미분
- [Color Processing](12-color-processing.md): 빛과 색의 삼원색, XYZ와 RGB 변환, HSV와 YIQ 색 모델, Pseudo Coloring, 컬러 영상 처리
- [13주차: Interpolation, Fourier Transform, 필터](13-week-13.md): Nearest와 Bilinear 비교, Cubic Interpolation, Fourier Transform의 성질, Ideal·Butterworth 필터를 그림 위주로 정리
