# Processamento Digitais de Imagens

## Introdução

>Está página consiste em reponder os questiónarios propostos ao decorrer do componente curricular de processamento digital de
>imagens (PDI). Tais atividades consistem em fixar o conteúdo aprendido em sala e colocar as técnicas adiquiridas em prática.

## Atividade 2

### Regions
Este primeiro exercício consistia em aprender os conceitos aprendidos sobre manipulação de pixels em uma imagem. Foi proposto que fosse implementado um programa chamdo regions.cpp. Esse programa deveria solicitar ao usuário as coordenadas de dois pontos P1 e P2, os quais são pontos opostos de um retângulo, localizados dentro dos limites do tamanho da imagem e fazer o negativo dessa imagem. 

O código do programa que realiza a operação é dado a seguir.
```css
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
    if(!image.data) {
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
```css
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



## Atividade 3

### Labeling

Código do programa.
```css
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
O programa usa a os tons disponíveis de cinza para contar a quantidade de bolhas. Como há somente 255 tons, ocorrerá uma redundância nas contagem em caso haver mais de 255 bolhas. Uma solução seria ampliar a resolução, isto é, aumentar a quantidade de tons de cinza. Outra solução é usar uma imagem colorida ao invés de em tons de cinza.



## Atividade 4

### Equalize

Código do programa.

```css
#include <iostream>
#include <opencv2/opencv.hpp>

using namespace cv;
using namespace std;

int main(int argc, char** argv){

  Mat image_antes, image_depois; //imagem antes e depois de equalizada

  int width, height;
  VideoCapture cap;
  int nbins = 64;
  float range[] = {0, 256};
  const float *histrange = { range };

  Mat hist_antes, hist_depois;  //onde serao armazenados os histogramas

  bool uniform = true;
  bool acummulate = false;

  cap.open(0);

  if(!cap.isOpened()){
    cout << "cameras indisponiveis";
    return -1;
  }

  width  = cap.get(CV_CAP_PROP_FRAME_WIDTH);
  height = cap.get(CV_CAP_PROP_FRAME_HEIGHT);

  cout << "largura = " << width << endl;
  cout << "altura  = " << height << endl;

  int histw = nbins, histh = nbins/2;
  Mat histImgA(histh, histw, CV_8UC3, Scalar(0,0,0));
  Mat histImgD(histh, histw, CV_8UC3, Scalar(0,0,0));

  while(1){
    cap >> image_antes;

    cvtColor(image_antes,image_antes,CV_BGR2GRAY); //converte a imagem para tons de cinza

    equalizeHist(image, image_depois); //equaliza a imagem


    //calcula os histogramas da imagem equalizda e da original
    calcHist(&image_antes, 1, 0, Mat(), hist_antes, 1,
             &nbins, &histrange,
             uniform, acummulate);
    calcHist(&image_depois, 1, 0, Mat(), hist_depois, 1,
             &nbins, &histrange,
             uniform, acummulate);
    //normaliza o histograma da imagem equalizada e o hisograma da original
    normalize(hist_antes, hist_antes, 0, histImgA.rows, NORM_MINMAX, -1, Mat());
    normalize(hist_depois, hist_depois, 0, histImgD.rows, NORM_MINMAX, -1, Mat());

    //zera as imagens dos histogramas
    histImgA.setTo(Scalar(0));
    histImgD.setTo(Scalar(0));


    for(int i=0; i<nbins; i++){
      //desenha o histograma da imagem original
      line(histImgA,
           Point(i, histh),
           Point(i, histh-cvRound(hist_antes.at<float>(i))),
           Scalar(0, 0, 255), 1, 8, 0);

      //desenha o histograma da imagem equalizada
      line(histImgD,
           Point(i, histh),
           Point(i, histh-cvRound(hist_depois.at<float>(i))),
           Scalar(0, 255, 0), 1, 8, 0);

    }
    histImgA.copyTo(image_antes(Rect(0, 0       ,nbins, histh)));
    histImgD.copyTo(image_depois(Rect(0, histh   ,nbins, histh)));

    imshow("antes", image_antes);
    imshow("depois", image_depois);
    if(waitKey(30) >= 0) break;
  }
  return 0;
}
```

