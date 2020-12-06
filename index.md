# **Processamento Digital de Imagens Usando Biblioteca OpenCV** 

# Parte 1

## **1.Negativo de uma Imagem**

**Esse programa solicita ao usuário as coordenadas de dois pontos P1 e P2 localizados dentro dos limites do tamanho de uma imagem e exibe que lhe for fornecida. A região definida pelo retângulo de vértices opostos definidos pelos pontos P1 e P2 será exibida com o negativo da imagem na região correspondente.**


### Código
_regions.cpp_


```
#include <iostream>
#include <opencv2/opencv.hpp>

using namespace cv;
using namespace std;
 
int main(int, char**){

Mat image;
int rows, cols;
int x1, y1;
int x2, y2;

image = imread("newton.jpg",CV_LOAD_IMAGE_GRAYSCALE);

if(!image.data)
   cout << "erro" << endl;

namedWindow("janela",WINDOW_AUTOSIZE);

 rows = image.rows;
 cols = image.cols;

 cout << "coordenada x da imagem vai de:  " << "0-" << rows-1 << endl;
 cout << "coordenada y da imagem vai de:  " << "0-" << cols-1 << endl;


 do{ 
    cout << "Digite a coordenada (x,y) do ponto P1: ";
    cout<< " "<<endl;
    cin >> x1;
    cout<< " "<<endl;
    cin >> y1;  
 }while(x1<0 || x1>rows || y1<0 || y1>cols);


 do{
    cout << "Digite a coordenada (x,y) do ponto P2: ";
    cout<< " "<<endl;
    cin>> x2;
    cout<< " "<<endl;
    cin>> y2;
 }while(x2<0 || x2>rows || y2<0 || y2>cols);


  for(int i=x1; i<x2; i++){
    for(int j=y1; j<y2; j++){
     image.at<uchar>(i,j)=255-image.at<uchar>(i,j);
   }
 }

  imshow("janela", image);
  waitKey();

  return 0;
}

```
O código inicialmente inclui as bibliotecas necessárias. Depois define os tipos a serem utilizados que são image e int (inteiros). Faz o teste para verificar se a imagem foi carregada correntamente (é utilizada a imagem em grayscale). A quantidade pixels passa a ser conhecida e portanto, a quantidade de linhas e colunas (rows, cols). É solicitado ao usuário os pontos e feito um teste para verificar se os pontos estão dentro dos limites da imagem, caso não, é solicitado novamente.

Calcula-se o negativo dos pixels da região delimitada pelos pontos fornecidos subtraindo de 255 o valor do tom de cinza associado a esse pixel. Isso é feito no seguinte trecho do código acima:
````
  for(int i=x1; i<x2; i++){
    for(int j=y1; j<y2; j++){
     image.at<uchar>(i,j)=255-image.at<uchar>(i,j);
   }
 }
 
````

**Imagem original - Entrada do programa:**

![Imagem Original](https://i.imgur.com/nN2xezH.jpg)


**Saída do programa _regions.cpp_**

![Imagem  Negativo](https://i.imgur.com/BUsF7S0.jpg)

## **2.Troca Regiões**

**Esse programa permuta o segundo com o quarto e o primeiro com o terceiro quadrantes de uma imagem.**

### Código
_trocaregioes.cpp_
````
#include <iostream>
#include <opencv2/opencv.hpp>
 
using namespace cv;
using namespace std;
 
int main(int, char**){

Mat image, auxiliar;
int x, y;

 image = imread("biel.png",CV_LOAD_IMAGE_GRAYSCALE);
  if(!image.data)
    cout << "erro" << endl;

  namedWindow("janela",WINDOW_AUTOSIZE);

  x = image.rows;
  y = image.cols;

  image(Rect(0,0,y/2,x/2)).copyTo(auxiliar);                             
  image(Rect(x/2,y/2,y/2,x/2)).copyTo(image(Rect(0,0,y/2,x/2)));     
  auxiliar.copyTo(image(Rect(x/2,y/2,y/2,x/2)));                          
  image(Rect(0,y/2,y/2,x/2)).copyTo(auxiliar);                            
  image(Rect(x/2,0,y/2,x/2)).copyTo(image(Rect(0,y/2,y/2,x/2)));     
  auxiliar.copyTo(image(Rect(x/2,0,y/2,x/2)));      
  

  imshow("janela", image);
  waitKey();

  return 0;

}

````
A troca de regiões ocorre nas seguintes linhas de código:

````
  image(Rect(0,0,y/2,x/2)).copyTo(auxiliar);                             
  image(Rect(x/2,y/2,y/2,x/2)).copyTo(image(Rect(0,0,y/2,x/2)));     
  auxiliar.copyTo(image(Rect(x/2,y/2,y/2,x/2)));                          
  image(Rect(0,y/2,y/2,x/2)).copyTo(auxiliar);                            
  image(Rect(x/2,0,y/2,x/2)).copyTo(image(Rect(0,y/2,y/2,x/2)));     
  auxiliar.copyTo(image(Rect(x/2,0,y/2,x/2)));    
  
````
De acordo com os seguintes passos:

Copia segundo quadrante para imagem auxiliar; Copia quarto quadrante para o segundo quadrante; Copia imagem auxiliar para o quarto quadrante; Copia primeiro quadrante para a imagem auxiliar; Copia terceiro quadrante para o primeiro quadrante; Copia imagem auxiliar para o terceiro quadrante.


**Entrada do programa _trocaregioes.cpp_**

![Entrada do Programa](https://i.imgur.com/7t6rRYq.jpg)

**Saída do programa _trocaregioes.cpp_**

![Entrada do Programa](https://i.imgur.com/0ioEm6X.jpg)


## **3.Identificando e contando bolhas**

**Esse programa identifica bolhas com ou sem buracos internos que existam na cena e conta bolhas que não tocam as bordas da imagem.**

### Código

_labeling.cpp_

````
#include <iostream>
#include <opencv2/opencv.hpp>
  
  using namespace cv;
  using namespace std;
  
  int main(int argc, char** argv){

   Mat image;
   int width, height;
   Vec3b branco(255,255,255);
   Vec3b preto(0,0,0);
   Vec3b cor_bolha_buraco(204,50,153); //cor
   CvPoint p;
   unsigned long int objetos=0;
   unsigned long int bolhas_buracos=0;
   unsigned long int bolhas=0;
   Vec3b label;
 
   image = imread("bolhas.png",CV_LOAD_IMAGE_COLOR);
 
   if(!image.data){
     cout << "erro ao carregar imagem\n";
     return(-1);
  }

   width=image.size().width;
   height=image.size().height;


 //**************** REMOVER BOLHAS DAS BORDAS ***************************

   //Remover bolhas da borda superior
   
  for(int j=0; j<width; j++){
    if(image.at<Vec3b>(0,j) == branco){
       p.x=j;
       p.y=0;
       floodFill(image,p,Scalar(0,0,0));
       }
     }
  imwrite("1_1.png", image);

  //*********************************************************************

  //Remover bolhas da borda lateral direita
  
   for(int i=0; i<height; i++){
    if(image.at<Vec3b>(i,width-1) == branco){
       p.x=width-1;
       p.y=i;
       floodFill(image,p,Scalar(0,0,0));
     }
   }
   imwrite("1_2.png", image);
 
   //Remover bolhas da borda inferior
  for(int j=0; j<width; j++){
     if(image.at<Vec3b>(height-1,j) == branco){
       p.x=j;
       p.y=height-1;
       floodFill(image,p,Scalar(0,0,0));
    }
   }
  imwrite("1_3.png", image);

 //*********************************************************************
   //Remover bolhas da borda lateral esquerda
   
   for(int i=0; i<height; i++){
    if(image.at<Vec3b>(i,0) == branco){
       p.x=0;
       p.y=i;
       floodFill(image,p,Scalar(0,0,0));
     }
   }
   imwrite("1_4.png", image);
  
 //*********************************************************************

   //Troca a cor do fundo
   
   p.x=0; p.y=0;
   floodFill(image,p,Scalar(100,100,100));
   imwrite("2.png", image);
 
 //********************** ACHA BOLHAS COM BURACOS **********************

  
   for(int i=0;i<height;i++){
     for(int j=0;j<width;j++){
     if(image.at<Vec3b>(i,j) == preto){
         if(image.at<Vec3b>(i,j-1) == cor_bolha_buraco){}
        else if(image.at<Vec3b>(i,j-1) == branco){
           p.x = j-1;
           p.y = i;
           floodFill(image,p,Scalar(204,50,153));
         }
      }
     }
   }
   imwrite("3.png", image);
   
 //*********************************************************************
 
    //Troca a cor do fundo 
    
   p.x=0; p.y=0;
   floodFill(image,p,Scalar(0,0,0));
   imwrite("4.png", image);

 //**************************** LABELING *******************************
 
   
   for(int i=0;i<height;i++){
     for(int j=0;j<width;j++){
        if(image.at<Vec3b>(i,j) == branco){
          p.x=j; p.y=i;
          objetos++;
          bolhas++;
          label[0] = objetos & 0x0000FF;
          label[1] = (objetos & 0x00FF00) >> 8;
          label[2] = (objetos & 0xFF0000) >> 16;
          floodFill(image,p,Scalar(label[0],label[1],label[2]));
        }
        else if(image.at<Vec3b>(i,j-1) == cor_bolha_buraco){
          p.x=j; p.y=i;
          objetos++;
          bolhas_buracos++;
          label[0] = (objetos & 0x0000FF);
          label[1] = (objetos & 0x00FF00) >> 8;
          label[2] = (objetos & 0xFF0000) >> 16;
          floodFill(image,p,Scalar(label[0],label[1],label[2]));
       }
     }
  }

  //********************************************************************

  cout << "Bolhas sem buracos: " << bolhas << endl;
  cout << "Bolhas com buracos: " << bolhas_buracos << endl;
  cout << "Total de bolhas: "    << objetos << endl;

 imwrite("labeling.png", image);
  waitKey();
 return 0;
}

````

**Entrada do programa _labeling.cpp_**

![Entrada do Programa](https://i.imgur.com/NS7jxHF.png)


Etapas:

 **1.Remoção das bolhas das bordas**

 1.1.Remoção das bolhas da borda superior

![1.1](https://i.imgur.com/wB64lB1.png)

 1.2.Remoção das bolhas da borda lateral direita

![1.2](https://i.imgur.com/nWIXQ4H.png)

 1.3.Remoção das bolhas da borda inferior

![1.3](https://i.imgur.com/lCurncU.png)

1.4.Remoção das bolhas da lateral esquerda

![1.4](https://i.imgur.com/SB7guki.png)

 **2.Troca da cor de fundo**
 
 ![2](https://i.imgur.com/WNhnWef.png)
 
 **3.Identifica as bolhas com buracos**
 
 ![3](https://i.imgur.com/p1oZw3z.png)

 **4.Troca da cor de fundo**
 
 ![4](https://i.imgur.com/ctR2Qsk.png)
 
  **5.Labeling - saída do programa**
 
 ![5](https://i.imgur.com/4cc9Vl3.png)
 
**Utilzação do programa (contagem das bolhas)**

![](https://i.imgur.com/k6P6X3K.jpg)


## **4.Equalizador de Histograma**

**Esse programa, para cada imagem capturada, realiza a equalização do histogram antes de exibir a imagem. Sua implementação aponta a câmera para ambientes com iluminações variadas e observando o efeito gerado. As imagens processadas serão em tons de cinza.**

### Código

_equalize.cpp_

````
#include <iostream>
#include <opencv2/opencv.hpp>
#include <unistd.h>


using namespace cv;
using namespace std;

int main(int argc, char** argv){
  VideoCapture cap;
  Mat frame_in, frame_out;

  cap.open(0);

  if(!cap.isOpened()){
    cout << "camera indisponivel";
    return -1;
  }

  int frame_width = cap.get(CV_CAP_PROP_FRAME_WIDTH);
  int frame_height = cap.get(CV_CAP_PROP_FRAME_HEIGHT);

  while(1){

    cap >> frame_in;
    cvtColor(frame_in,frame_in, CV_BGR2GRAY);
  
    equalizeHist(frame_in,frame_out);

    namedWindow("Original", CV_WINDOW_AUTOSIZE );
    namedWindow("Equalizado", CV_WINDOW_AUTOSIZE );

    imshow("Original", frame_in );
    imshow("Equalizado", frame_out );

    char e = (char)waitKey(33);
    if(e == 32) break;         //space
    
  }

  return 0;
}

````

 **Link para Vídeo mostrando a aplicação do programa:**
 
[Equalização de Histograma - YouTube](http://www.youtube.com/watch?v=CophjRFXGWI)
 

 
## **5.Detector de Movimento**

**Este programa continuamente calcula o histograma da imagem  e o compara com o último histograma calculado. Quando a diferença entre estes ultrapassa um limiar pré-estabelecido, ativa um alarme.**

### Código

_motiondetector.cpp_

````
#include <iostream>
#include <opencv2/opencv.hpp>
#include <unistd.h>

using namespace cv;
using namespace std;

int main(int argc, char** argv){

  Mat image1, image2, sinal_alarme;
  Mat histograma1, histograma2;
  int nbins = 64;
  float range[] = {0, 256};
  const float *histrange = { range };
  VideoCapture cap;
  bool uniform = true;
  bool acummulate = false;
  float correlation;

  sinal_alarme = imread("alerta.jpg",CV_LOAD_IMAGE_GRAYSCALE);

  cap.open(0);

  if(!cap.isOpened()){
    cout << "falha camera";
    return -1;
  }

  while(1){
    cap >> image1;
    cvtColor(image1,image1, CV_BGR2GRAY);
    calcHist(&image1, 1, 0, Mat(), histograma1, 1, &nbins,
             &histrange, uniform, acummulate);

    usleep(10000);

    cap >> image2;
    cvtColor(image2,image2, CV_BGR2GRAY);
    calcHist(&image2, 1, 0, Mat(), histograma2, 1, &nbins,
             &histrange, uniform, acummulate);

    correlation = compareHist(histograma1, histograma2,
                              CV_COMP_CORREL);

    if (correlation < 0.95){
      cout << "***Movimento Detectado***" << endl;
    }


    imshow("image", image1);

    char encerrar = (char)waitKey(33);
    if( encerrar == 32 ) break; //space
  }

  return 0;
}

````
 
 **Link para Vídeo mostrando a aplicação do programa:**
 
[Detector de Movimento - YouTube](https://www.youtube.com/watch?v=f6WjwWiZSYk)
 
# Parte 2

## **7.Filtro Homomórfico**

**Implementação de filtro homomórfico para melhorar imagens com iluminação irregular. Criando uma cena mal iluminada e ajustando os parâmetros do filtro homomórfico para corrigir a iluminação da melhor forma possível. As imagens fornecidas são em tons de cinza.**

### Código

_homomosphicfilter.cpp_

````
#include <iostream>
#include <opencv2/opencv.hpp>
#include <opencv2/imgproc/imgproc.hpp>

#define RADIUS 20

#define MAX_GAMA_H  100.0
#define MAX_GAMA_L  100.0
#define MAX_D0      100.0
#define MAX_C       100.0

using namespace cv;
using namespace std;

int gama_H;
int gama_L;
int d0;
int c;

int dft_M, dft_N;
Mat filter, tmp;

void on_filter(int, void*)
{
  float _d0 =    ((float) 2*d0/ (float) MAX_D0) * sqrt( dft_M*dft_M + dft_N*dft_N )/2;
  float _gamaL = ((float) 10*gama_L)/ (float) MAX_GAMA_L;
  float _gamaH = ((float) 10*gama_H)/ (float) MAX_GAMA_H;
  float _c =     ((float) 10*c)/ (float) MAX_C;

  cout << "d0: " << _d0 << "  L: " << _gamaL << "  H: "  << _gamaH << "  c: " << _c << endl;

  double n1, n2;
  double n = 0.0;
  double dGamas = _gamaH - _gamaL;
  double d02 = _d0*_d0;

  for(int i=0; i<dft_M; i++)
  {
    for(int j=0; j<dft_N; j++)
    {
        n1 = (i - dft_M/2)*(i - dft_M/2);
        n2 = (j - dft_N/2)*(j - dft_N/2);
        n = _c * (n1 + n2);

        tmp.at<float>(i,j) = dGamas * (1 - exp(-n/d02)) + _gamaL;
    }
  }

  Mat comps[]= {tmp, tmp};
  merge(comps, 2, filter);
}


// troca os quadrantes da imagem da DFT
void deslocaDFT(Mat& image )
{
  Mat aux, A, B, C, D;

  // se a imagem tiver tamanho impar, recorta a regiao para
  // evitar cópias de tamanho desigual
  image = image(Rect(0, 0, image.cols & -2, image.rows & -2));
  int cx = image.cols/2;
  int cy = image.rows/2;
  
  // reorganiza os quadrantes da transformada
  // A B   ->  D C
  // C D       B A
  A = image(Rect(0, 0, cx, cy));
  B = image(Rect(cx, 0, cx, cy));
  C = image(Rect(0, cy, cx, cy));
  D = image(Rect(cx, cy, cx, cy));

  A.copyTo(aux);  D.copyTo(A);  aux.copyTo(D); // A <-> D
  C.copyTo(aux);  B.copyTo(C);  aux.copyTo(B); // C <-> B
}

int main(int , char**)
{
  VideoCapture cap;   
  Mat imaginaryInput, complexImage, multsp;
  Mat padded, mag;
  Mat image, imagegray; 
  Mat_<float> realInput, zeros;
  vector<Mat> planos;
  
  char key;   // guarda tecla capturada

  cap.open(0);  // abre a câmera default
  if(!cap.isOpened()) return -1;

  // captura uma imagem para recuperar as
  // informacoes de gravação
  cap >> image;

  // identifica os tamanhos otimos para
  // calculo do FFT
  dft_M = getOptimalDFTSize(image.rows);
  dft_N = getOptimalDFTSize(image.cols);

  // realiza o padding da imagem
  copyMakeBorder(image, padded, 0,
                 dft_M - image.rows, 0,
                 dft_N - image.cols,
                 BORDER_CONSTANT, Scalar::all(0));

  // parte imaginaria da matriz complexa (preenchida com zeros)
  zeros = Mat_<float>::zeros(padded.size());

  // prepara a matriz complexa para ser preenchida
  complexImage = Mat(padded.size(), CV_32FC2, Scalar(0));

  // a função de transferência (filtro frequencial) deve ter o
  // mesmo tamanho e tipo da matriz complexa
  filter = complexImage.clone();

  // cria uma matriz temporária para criar as componentes real
  // e imaginaria do filtro ideal
  tmp = Mat(dft_M, dft_N, CV_32F);

  char title[25];
  namedWindow("filtrada", 1);

  sprintf       ( title, "Gama H x %.1lf", MAX_GAMA_H );
  createTrackbar( title, "filtrada",   &gama_H, MAX_GAMA_H, on_filter );

  sprintf       ( title, "Gama L x %.1lf", MAX_GAMA_L );
  createTrackbar( title, "filtrada",   &gama_L, MAX_GAMA_L, on_filter );
  
  sprintf       ( title, "D0 x %.1lf",  MAX_D0 );
  createTrackbar( title, "filtrada", &d0, MAX_D0, on_filter );

  sprintf       ( title, "c x %.1lf",   MAX_C );
  createTrackbar( title, "filtrada", &c, MAX_C, on_filter );

  on_filter(0, 0);


  for(;;){
    cap >> image;
    cvtColor(image, imagegray, CV_BGR2GRAY);
    imshow("original", imagegray);

    // realiza o padding da imagem
    copyMakeBorder(imagegray, padded, 0,
                   dft_M - image.rows, 0,
                   dft_N - image.cols,
                   BORDER_CONSTANT, Scalar::all(0));

    // limpa o array de matrizes que vao compor a
    // imagem complexa
    planos.clear();
    // cria a compoente real
    realInput = Mat_<float>(padded);
    // insere as duas componentes no array de matrizes
    planos.push_back(realInput);
    planos.push_back(zeros);


    realInput = realInput + Scalar::all(1);
    log(realInput,realInput);

    // combina o array de matrizes em uma unica
    // componente complexa
    merge(planos, complexImage);

    // calcula o dft
    dft(complexImage, complexImage);

    // realiza a troca de quadrantes
    deslocaDFT(complexImage);

    // aplica o filtro frequencial
    mulSpectrums(complexImage,filter,complexImage,0);

    // limpa o array de planos
    planos.clear();
    // separa as partes real e imaginaria para modifica-las
    split(complexImage, planos);
 
    // usa o valor medio do espectro para dosar o ruido 
    
    // recompoe os planos em uma unica matriz complexa
    merge(planos, complexImage);

    // troca novamente os quadrantes
    deslocaDFT(complexImage);

    // calcula a DFT inversa
    idft(complexImage, complexImage);

    // limpa o array de planos
    planos.clear();

    // separa as partes real e imaginaria da
    // imagem filtrada
    split(complexImage, planos);

    // normaliza a parte real para exibicao
    normalize(planos[0], planos[0], 0, 1, CV_MINMAX);
    exp(planos[0],planos[0]);
    normalize(planos[0], planos[0], 0, 1, CV_MINMAX);
    imshow("filtrada", planos[0]);
 
    key = (char) waitKey(10);
    if(key == 27) break;
 
  }
  return 0;
} 

````

 **Link para Vídeo mostrando a aplicação do programa:**
 
[Filtro Homomórfico - YouTube](https://www.youtube.com/watch?v=lo8H_BX-_Xc)
 
 
## **8.Imagem pontilhista melhorada pelo algoritmo de Canny**

Implementação de um programa cannypoints.cpp. A ideia é usar as bordas produzidas pelo algoritmo de Canny para melhorar a qualidade da imagem pontilhista gerada. A forma como a informação de borda é usada:

1.Desenha pontos grandes na imagem pontilhista básica;

2.Usar a posição dos pixels de borda encontrados pelo algoritmo de Canny para desenhar pontos nos respectivos locais na imagem gerada e nas outras partes da imagem (sem bordas);

3.Aumenta-se os limiares do algoritmo de Canny e, para cada novo par de limiares, desenhar círculos cada vez menores nas posições encontradas.


_cannypoints.cpp_

````

#include <iostream>
#include <opencv2/opencv.hpp>
#include <fstream>
#include <iomanip>
#include <vector>
#include <algorithm>
#include <numeric>
#include <ctime>
#include <cstdlib>

using namespace std;
using namespace cv;

int STEP = 5;
int JITTER = 3;
int RAIO = 3;

int top_slider = 10;
int top_slider_max = 200;

char TrackbarName[50];

Mat image, border;

int main(int argc, char** argv){
  vector<int> yrange;
  vector<int> xrange;

  Mat frame, points;

  int width, height;
  int x, y;



 image = imread("flower.jpg");
  if(!image.data)
    cout << "erro" << endl;



  width=image.size().width;
  height=image.size().height;

  xrange.resize(height/STEP);
  yrange.resize(width/STEP);

  iota(xrange.begin(), xrange.end(), 0);
  iota(yrange.begin(), yrange.end(), 0);

  for(uint i=0; i<xrange.size(); i++){
    xrange[i]= xrange[i]*STEP+STEP/2;
  }

  for(uint i=0; i<yrange.size(); i++){
    yrange[i]= yrange[i]*STEP+STEP/2;
  }

  points = Mat(height, width, CV_8UC3, Scalar(255, 255, 255));

  random_shuffle(xrange.begin(), xrange.end());

  for(auto i : xrange){
    random_shuffle(yrange.begin(), yrange.end());
    for(auto j : yrange){
      x = i+rand()%(2*JITTER)-JITTER+1;
      y = j+rand()%(2*JITTER)-JITTER+1;
      Vec3b val = image.at<Vec3b>(x,y);
      circle(points,
             cv::Point(y,x),
             RAIO,
             CV_RGB(val[2],val[1],val[0]),
             -1,
             CV_AA);
    }
  }

  imshow("pontilhismo", points);
  imwrite("pontilhismo.jpg", points);

  Canny(image, border, 30, 3*120);
  
  for(auto i : xrange){
    //random_shuffle(yrange.begin(), yrange.end());
    for(auto j : yrange){
      if(border.at<uchar>(i,j) == 255){
        x = i+rand()%(2*JITTER)-JITTER+1;
        y = j+rand()%(2*JITTER)-JITTER+1;
        RAIO = 3;
      }else{
        x = i;
        y = j;
        RAIO = 5;
      }

      Vec3b val = image.at<Vec3b>(x,y);
      circle(points,
      cv::Point(y,x),
      RAIO,
      CV_RGB(val[2],val[1],val[0]),
     -1,
     CV_AA);
    }
  }

  //exibe as imagens

  imshow("canny", border);
  imshow("cannypoints", points);
  waitKey();

  imwrite("canny.jpg", border);
  imwrite("cannypoints.jpg", points);

    
  return 0;
}

````

**Entrada do programa _cannypoints.cpp_**

![](https://i.imgur.com/NfJbA63.jpg)

![](https://i.imgur.com/5JRtgfF.jpg)

![](https://i.imgur.com/FM7L7fU.jpg)

**Saída do programa _cannypoints.cpp_**

![](https://i.imgur.com/4H35cmA.jpg)

## **9.K-means**

Programa onde a execução do códig usa o parâmetro nRodadas=1 e inciar os centros de forma aleatória usando o parâmetro KMEANS_RANDOM_CENTERS. Realiza 10 rodadas diferentes do algoritmo.

_kmeans.cpp_

````
#include <opencv2/opencv.hpp>
#include <cstdlib>
#include <string>
#include <iostream>
using namespace std;
using namespace cv;

int main( int argc, char** argv ){
  int nClusters = 6;
  Mat rotulos;
  int nRodadas = 1;
  Mat centros;
  int cont=1;


  
  Mat img = imread("quimica.jpeg");
  if(!img.data)
    cout << "erro" << endl;


  Mat samples(img.rows * img.cols, 3, CV_32F);

  while(cont <=10){
    for( int y = 0; y < img.rows; y++ ){
      for( int x = 0; x < img.cols; x++ ){
        for( int z = 0; z < 3; z++){
          samples.at<float>(y + x*img.rows, z) = img.at<Vec3b>(y,x)[z];
        }
      }
    }

    kmeans(samples,
       nClusters,
       rotulos,
       TermCriteria(CV_TERMCRIT_ITER|CV_TERMCRIT_EPS, 10000, 0.0001),
       nRodadas,
       KMEANS_RANDOM_CENTERS,
       centros );


    Mat rotulada( img.size(), img.type() );
    for( int y = 0; y < img.rows; y++ ){
      for( int x = 0; x < img.cols; x++ ){
        int indice = rotulos.at<int>(y + x*img.rows,0);
        rotulada.at<Vec3b>(y,x)[0] = (uchar) centros.at<float>(indice, 0);
        rotulada.at<Vec3b>(y,x)[1] = (uchar) centros.at<float>(indice, 1);
        rotulada.at<Vec3b>(y,x)[2] = (uchar) centros.at<float>(indice, 2);
      }
    }
    stringstream ss;
    ss << "saida" << cont << ".jpg";
    string s = ss.str();
    cont++;

    imshow( "clustered image", rotulada );
    imwrite(s, rotulada);
    waitKey( 1 );
  }
  return 0;
}

````
Rodada 1
![1](https://i.imgur.com/ubmY6oW.jpg)
Rodada 2
![2](https://i.imgur.com/xID3uFX.jpg)
Rodada 3
![3](https://i.imgur.com/MfdJQKB.jpg)
Rodada 4
![4](https://i.imgur.com/4ldBd2S.jpg)
Rodada 5
![5](https://i.imgur.com/65TIY3B.jpg)
Rodada 6
![6](https://i.imgur.com/Sn4a6m9.jpg)
Rodada 7
![7](https://i.imgur.com/ll7z4Xv.jpg)
Rodada 8
![8](https://i.imgur.com/nKODAXA.jpg)
Rodada 9
![9](https://i.imgur.com/EJ7gviL.jpg)
Rodada 10
![10](https://i.imgur.com/rShsBRv.jpg)

