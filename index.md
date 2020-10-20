# **Processamento Digital de Imagens** 




## **1.Regions**
**Esse programa solicita ao usuário as coordenadas de dois pontos P1 e P2 localizados dentro dos limites do tamanho de uma imagem e exibe que lhe for fornecida. Entretanto, a região definida pelo retângulo de vértices opostos definidos pelos pontos P1 e P2 será exibida com o negativo da imagem na região correspondente.**


```
#include <iostream>

#include <opencv2/opencv.hpp>

int main(int argc, char** argv){
  cv::Mat image;
  image = cv::imread(argv[1],cv::IMREAD_GRAYSCALE);
  cv::imshow("image", image);
  cv::waitKey();
  return 0;
}```
