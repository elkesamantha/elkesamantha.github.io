# **Processamento Digital de Imagens Usando Biblioteca OpenCV** 


## **1.Regions**
**Esse programa solicita ao usuário as coordenadas de dois pontos P1 e P2 localizados dentro dos limites do tamanho de uma imagem e exibe que lhe for fornecida. Entretanto, a região definida pelo retângulo de vértices opostos definidos pelos pontos P1 e P2 será exibida com o negativo da imagem na região correspondente.**

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


  for(int i=x1;i<x2;i++){
    for(int j=y1;j<y2;j++){
     image.at<uchar>(i,j)=255-image.at<uchar>(i,j);
   }
 }

  imshow("janela", image);
  waitKey();

  return 0;
}

```

