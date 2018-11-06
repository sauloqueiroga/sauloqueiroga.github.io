# Processamento Digitais de Imagens

## Introdução

Está página consiste em reponder os questiónarios propostos ao decorrer do componente curricular de processamento digital de imagens (PDI). Tais atividades consistem em fixar o conteúdo aprendido em sala e colocar as técnicas adiquiridas em prática.

## Atividade 2

### Regions
Este primeiro exercício consistia em fixar os conceitos aprendidos sobre manipulação de pixels em uma imagem. Foi proposto que fosse implementado um programa chamado regions.cpp. Esse programa deveria solicitar ao usuário as coordenadas de dois pontos P1 e P2, os quais são pontos opostos de um retângulo, localizados dentro dos limites do tamanho da imagem e fazer o negativo dessa imagem. Para fazer o negativo de uma região, fazemos com que o pixel da posição (i,j) receba 255, que é a tonalidade máxima, subtraído da tonalidade do pixel atual.

O código do programa que realiza a operação é dado a seguir.
```cpp
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

O segundo exercício da atividade 2 consistia em dividir uma imagem em quatro partes de tamanhos iguais e em seguida permutar os quadrantes na diagonal, ou seja, o primeiro com o quarto e o segundo com o terceiro. Para isso, utilizamos propriedades da classe "Mat".
O código do programa que executa esse procedimento é dado à seguir:

```cpp
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
    //regiao inferior esquerda = terceiro quadrate
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
 
O programa base sugerido na atividade usa a os tons disponíveis de cinza para contar a quantidade de bolhas. No primeiro exercício desta atividade occorrerá um erro quando existir uma quantidade muito grande de regiões, pois há somente 255 tons,isto é, ocorrerá uma redundância nas contagem em caso haver mais de 255 bolhas. Uma solução seria ampliar a resolução, isto é, aumentar a quantidade de tons de cinza. Outra solução é usar uma imagem colorida ao invés de em tons de cinza.



### Labeling

No segundo exercício da atividade 3, foi proposto que trabalhássemos com o algoritmo de floodfill para contar a quantidade de de regiões com ou sem buracos contidos na imagem bolhas.png. Para realizar a contagem de tais regiões foi implementado o código a seguir.

```cpp
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


## Atividade 4

### Equalize

Na atividade 4 colocamos em prática a importância dos histogramas nas técnicas de processamento digital de imagens. No primeiro exercício da atividade 4, utilizou-se como base o programa histogram.cpp para implementar um programa que realizasse a equalização do histograma de uma imagem.
Código do programa:

```cpp
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
## Atividade 7
Nesta atividade foi proposto fazer um filtro homomórfico para corrigir os efeitos de uma má iluminação em uma imagem digital. Esse filtro tem por objetivo de atenuar as baixas-frequências e realçar as altas. O filtro homomórfico funciona de uma forma que cada pixel da imagem pode ser separado em componentes distintas de iluminância e reflectância. A iluminância representa a quantidade de luz que incide sobre o pixel e apresenta variações lentas (baixa frequência = gammaL). Já a reflectância indica quanto dessa luz incidente é refletida e apresenta variações mais rápidas (alta frequência = gammaH). 

O filtro homomórfico pode ser descrito da seguinte maneira: 

![formula](https://user-images.githubusercontent.com/28017762/48094794-4033a480-e1fa-11e8-8874-d05143e45917.png)

O código do programa para implementar esse filtro foi baseado no dft.cpp e segue a baixo.
```cpp
#include <iostream>
#include <opencv2/opencv.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <math.h>

#define RADIUS 20

using namespace cv;
using namespace std;

// troca os quadrantes da imagem da DFT
void deslocaDFT(Mat& image ){
  Mat tmp, A, B, C, D;

  // se a imagem tiver tamanho impar, recorta a regiao para
  // evitar cÃ³pias de tamanho desigual
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

  // A <-> D
  A.copyTo(tmp);  D.copyTo(A);  tmp.copyTo(D);

  // C <-> B
  C.copyTo(tmp);  B.copyTo(C);  tmp.copyTo(B);
}



int main(int , char**){
  VideoCapture cap;
  Mat imaginaryInput, complexImage, multsp;
  Mat padded, filter, mag;
  Mat image, imagegray, tmp;
  Mat_<float> realInput, zeros;
  vector<Mat> planos;

  // guarda tecla capturada
  char key;
  
  // parametros do filtro homomórfico da imagem
  float gammaH, gammaL, d0, c;
  
  // valores ideais dos tamanhos da imagem
  // para calculo da DFT
  int dft_M, dft_N;

  // abre a camera default
  cap.open(0);
  if(!cap.isOpened())
    return -1;

  // captura uma imagem para recuperar as
  // informacoes de gravacao
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

  // a funcao de transferencia (filtro frequencial) deve ter o
  // mesmo tamanho e tipo da matriz complexa
  filter = complexImage.clone();

  // cria uma matriz temporaria para criar as componentes real
  // e imaginaria do filtro ideal
  tmp = Mat(dft_M, dft_N, CV_32F);

  // stados os valores iniciais dos parametros
  gammaL = 5.5;
  gammaH = 5.5;
  d0 = 10;
  c  = 0.5;

  for(;;){
    // prepara o filtro homomorfico
    for(int i=0; i<dft_M; i++){
      for(int j=0; j<dft_N; j++){
        tmp.at<float> (i,j) = (gammaH - gammaL)*(1 - exp(-c*(( pow((i-dft_M/2),2) + pow((j-dft_N/2),2))) / (pow(d0,2)) ))) + gammaL;
      }
    }

    // cria a matriz com as componentes do filtro e junta
    // ambas em uma matriz multicanal complexa
    Mat comps[]= {tmp, tmp};
    merge(comps, 2, filter);
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

    // recompoe os planos em uma unica matriz complexa
    merge(planos, complexImage);

    // troca novamente os quadrantes
    deslocaDFT(complexImage);

	 //cout << complexImage.size().height << endl;
    // calcula a DFT inversa
    idft(complexImage, complexImage);

    // limpa o array de planos
    planos.clear();

    // separa as partes real e imaginaria
    split(complexImage, planos);

    // normaliza a parte real para exibicao
    normalize(planos[0], planos[0], 0, 1, CV_MINMAX);
    imshow("filtrada", planos[0]);

    key = (char) waitKey(10);
    if( key == 27 ) break; // esc pressed!
    switch(key){
      case 'q':
        gammaH+=0.1;

        break;
      case 'a':
        gammaH-=0.1;

        break;
      case 'w':
        gammaL+=0.1;

        break;
      case 's':
        gammaL-=0.1;

        break;
      case 'r':
        d0+=0.1;

        break;
      case 'f':
        d0-=0.1;

        break;
      case 'e':
        c+=0.1;

        break;
      case 'd':
        c-=0.1;
        break;
    }
    cout << "parametros: " << endl;
    cout << "compoente de alta frequencia(gammaH): " << gammaH << endl;
    cout << "compoente de baixa frequencia(gammaL): " << gammaL <<endl;
    cout << " c: " << c << endl;
    cout << " Do: " << d0 << endl;
  }

  return 0;
}
```

## Atividade 8
Nesta atividade, foi pedido para implementar um algoritmo baseado na técnica de pontilhismoe e no detecção de bordas por canny.

O código do programa é dado a seguir.
```cpp
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

  #define STEP 5
  #define JITTER 3
  #define RAIO 5

  int main(int argc, char** argv){
  vector<int> yrange;
  vector<int> xrange;
  
  Mat Original, borderOriginalImage;
  Mat Pontilhismo;    
  
  
  int width, height, gray;
  int x, y;
  
  Original= imread("imagem.jpg" ,CV_LOAD_IMAGE_GRAYSCALE);
  
  srand(time(0));  
  
  if(!Original.data){
	cout << "nao abriu" << argv[1] << endl;
    cout << argv[0] << " imagem.jpg";
    exit(0);
  }
  
  width = Original.size().width;
  height = Original.size().height;
  
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

  Original.copyTo(Pontilhismo);

  //Pontilhismo
  for(auto i : xrange){
    random_shuffle(yrange.begin(), yrange.end());
    for(auto j : yrange){
      x = i+rand()%(2*JITTER)-JITTER+1;
      y = j+rand()%(2*JITTER)-JITTER+1;
      gray = Original.at<uchar>(x,y);
      circle(Pontilhismo,
             cv::Point(y,x),
             RAIO,
             CV_RGB(gray,gray,gray),
             -1,
             CV_AA);
    }
  }

  imshow("Imagem Pontilhista", Pontilhismo);
  imwrite("imagemPontilhista.jpg", Pontilhismo);

   //Canny
   for(int z=0; z<5; z++){
     Canny(Original, borderOriginalImage, 10*z, 50*z);
     int raio = 5-z;

     for(int i=0; i<height; i++ ){
        for(int j=0; j<width; j++){
           if(borderOriginalImage.at<uchar>(i,j) == 255){
              gray = Original.at<uchar>(i,j);
              circle(Pontilhismo,
                     cv::Point(j,i),
                     raio,
                     CV_RGB(gray,gray,gray),
                     -1,
                     CV_AA);
             }
        }
    }
  }
  
  imshow("PontilhismoComCanny", Pontilhismo);
  imwrite("imagemPontilhistaComCanny.jpg", Pontilhismo);


   waitKey();
  return 0;
}
```

