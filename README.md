# VIPHPC

To begin, research was done for weeks involving HPC using open ACC. This was for a research course at the University of Delaware. The start of this began with a simple ACC matrix multiplication. No parallelization was involved and it operated as normal. Luckily with OpenACC, we can take functions like these and make them quicker to run.

The "start" file was found online and can be found from many resources as long as you have the time to google. The "end" file is where it became more involved.

My logic behind the way I executed this was that loops are always the most ideal place to parallize.

To start, we need to initialize the matrix. That is where the following lines of code were used:

#define SIZE 5000

double a[SIZE][SIZE];
double b[SIZE][SIZE];
double c[SIZE][SIZE];
double d[SIZE][SIZE];

The main function was to then form the matricies:

int main()
{
  int i,j,k;
  double t1, t2;
  double tmp;
  
  for (i = 0; i < SIZE; ++i) {
    for (j = 0; j < SIZE; ++j) {
      a[i][j] = (double)(i + j);
      b[i][j] = (double)(i - j);
      c[i][j] = 0.0f;
      d[i][j] = 0.0f;
    }
  }
  
The parallelization came in:

#pragma acc data copyin(a,b) copy(c)
#pragma acc kernels
#pragma acc loop tile(32,32)
  for (i = 0; i < SIZE; ++i) {
    for (j = 0; j < SIZE; ++j) {
      tmp=0.0f;
#pragma acc loop reduction(+:tmp)
      for (k = 0; k < SIZE; ++k) {
        tmp += a[i][k] * b[k][j];
      }
      c[i][j] = tmp;
    }
  }
  
  
Overall, better results were seen with bigger numbers. You were able to get the idea of the timing when more data was being processed. The GPU ran better than the CPU as expected. With a prama just in the main, there was about a 21 x speed up and a 140 x speed up in both functions and loops. 
