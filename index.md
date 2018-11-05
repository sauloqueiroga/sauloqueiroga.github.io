#                                            Processamento Digitais de Imagens

## Atividade 1

### Regions
```sh
#include <iostream>
#include <opencv2/opencv.hpp>

using namespace cv;
using namespace std;

struct ponto{
    int x, y;     // coordenada X e Y da imagem
};

int main(int argc, char**argv)
{
    Mat image;  // variavel que recebera a imagem
    struct ponto p1, p2; //pontos que serao recebidos

    image = imread(argv[1], CV_LOAD_IMAGE_GRAYSCALE); //carrega a imagem em tons de cinza
    if(!image.data){
        cout << "Erro ao carregar a imagem." << endl;
        return 0;
    }
    
    imshow("janela", image); // mostra a imagem na janela "janela"
    waitKey();

    // recebe as coordenadas dos pontos
    cout << "coodenada x de p1: " << endl;
    cin >> p1.x;
    cout << "coodenada y de p1: " << endl;
    cin >> p1.y;
    cout << "coodenada x de p2: " << endl;
    cin >> p2.x;
    cout << "coodenada y de p2: " << endl;
    cin >> p2.y;

    // em caso dos pontos inseridos estarem fora da imagem, pedira para inserir novos pontos
    while(((p1.x || p2.x) > image.rows) || ((p1.y || p2.y) > image.cols) || ((p1.x || p2.x) < 0) || ((p1.y || p2.y) < 0)){
        cout << "Pontos fora dos limites da imagem. Insira-os novamente." << endl;
        cout << "coodenada x de p1: " << endl;
        cin >> p1.x;
        cout << "coodenada y de p1: " << endl;
        cin >> p1.y;
        cout << "coodenada x de p2: " << endl;
        cin >> p2.x;
        cout << "coodenada y de p2: " << endl;
        cin >> p2.y;
    }

    //fara o negativo do primeiro ponto ate o segundo (P = 255-P) formando um retangulo
    for(int i = p1.x; i < p2.x; i++){
        for(int j = p1.y; j < p2.y; j++){
            image.at<uchar>(i,j) = 255 - image.at<uchar>(i,j);
        }
    }

    imshow("janela", image); // mostra a imagem alterada na janela;
    waitKey();
    return 0;
}
```

### Troca Regiões

O código do programa troca regiões é dado a seguir.
```sh
#include <iostream>
#include <opencv2/opencv.hpp>

using namespace cv;
using namespace std;

int main(int argc, char**argv)
{
    Mat image;  // variavel que recebera a imagem
    uchar auxiliar;

    image = imread("biel.png", CV_LOAD_IMAGE_GRAYSCALE); //carrega a imagem em tons de cinza
    if(!image.data){
        cout << "Erro ao carregar a imagem." << endl;
        return 0;
    }
    
    imshow("janela", image); // mostra a imagem na janela "janela"
    waitKey();


    //regiao superior esquerda = primeiro quadrante
    //regiao superior direita = segundo quadrante
    //regiao infeior esquerda = terceiro quadrate
    //regiao inferior direita = quarto quadrante
    for(int i = 0 ; i < image.rows/2 ; i++){
        for(int j = 0 ; j < image.rols/2 ; j++){
            auxiliar = image.at<uchar>(i,j);                                            //guarda o valor do pixel atual do primeiro quadrante
            image.at<uchar>(i,j) = image.at<uchar>(i + image.rows/2, j + image.cols/2); //passa o valor do pixel correspondente do quarto quadrante para o do primeiquadrante
            image.at<uchar>(i + image.rows/2, j + image.cols/2) = aux;                  //o pixel do quarto quadrante recebe o valor da variavel auxiliar (valor do pixel do primeiro quadrante)



    for(int i = image.rows/2 ; i < image.rows ; i++){
        for(int j = 0 ; j < image.rols/2 ; j++){
            auxiliar = image.at<uchar>(i,j);                                            //guarda o valor do pixel atual do terceiro quadrante
            image.at<uchar>(i,j) = image.at<uchar>(i - image.rows/2, j + image.cols/2); //passa o valor do pixel correspondente do segundo quadrante para o do terceiro
            image.at<uchar>(i - image.rows/2, j + image.cols/2) = auxiliar;             //o pixel do segundo quadrante recebe o valor da variavel auxiliar (valor do pixel do terceiro quadrante)

    }


    imshow("janela", image); // mostra a imagem alterada na janela;
    waitKey();
    return 0;
}
```

## Atividade 2

### Labeling



Código da atividade 2.

```sh
#include <iostream>
#include <opencv2/opencv.hpp>

using namespace cv;
using namespace std;

int main(int argc, char **argv){
	Mat image;
	int obj = 0, bolhas = 0;
	CvPoint p;

	image = imread("bolhas.png",CV_LOAD_IMAGE_GRAYSCALE);
	if(!image.data){
		cout << "Erro ao carregar a imagem." << endl;
		return 0;
	}

	// esses dois laços retiram as bolhas das bordas
	for(int i = 0; i < image.rows; i++){
		if(image.at<uchar>(i, 0) == 255){
			p.x = 0;
			p.y = i;
			floodFill(image,p,0); // pinta as bolhas de preto na lateral esquerda
		}
        
		if(image.at<uchar>(i,image.cols - 1) == 255){
			p.x = image.cols - 1;
			p.y = i;
			floodFill(image,p,0); // pinta as bolhas de preto na lateral direita
		}
	}
    
	for(int i=0; i<image.cols; i++){
		if(image.at<uchar>(0,i) == 255){
			p.x = i;
			p.y = 0;
			floodFill(image,p,0);   // pinta as bolhas de preto na borda superior
		}
        
		if(image.at<uchar>(image.rows - 1, i) == 255){
			p.x = i;
			p.y = image.rows - 1;
			floodFill(image,p,0);   // pinta as bolhas de preto na borda inferior
		}
	}



	p.x = 0;
	p.y = 0;
	floodFill(image,p,254);     //muda a cor do fundo da imagem



	for(int i = 0; i < image.rows; i++)
		for(int j = 0; j < image.cols; j++){
			if(image.at<uchar>(i, j) == 255){
				p.x = j;
				p.y = i;
				floodFill(image,p,++obj);
			}
			if(image.at<uchar>(i,j) == 0){
				bolhas++;
				p.x = j;
				p.y = i;
				floodFill(image,p,128);
			}
		}

	imshow("image", image); // mostra a imagem
    waitKey();
  return 0;
}
```
## Atividade 3
```sh

```
## Atividade 4
```sh

```

