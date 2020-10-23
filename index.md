# **Processamento Digital de Imagens Usando Biblioteca OpenCV** 

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
Por fim, é exibida o negativo da imagem no trecho solicitado.


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
 

 
## **4.Detector de Movimento**

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
 


