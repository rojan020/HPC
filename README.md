
# Assessment overview
This portfolio is split up into 4 separate tasks which will test your knowledge of advanced multithreading and GPGPU programming using CUDA. Each task should be zipped up into a single zip folder containing all C/CUDA and resource files for the submission on Canvas.
## 1.	Password cracking using multithreading (15% - 100 marks)
In this task, you will be asked to use the “crypt” library to decrypt a password using multithreading. You will be provided with two programs. The first program called “EncryptSHA512.c” which allows you to encrypt a password. For this assessment, you will be required to decrypt a 4-character password consisting of 2 capital letters, and 2 numbers. The format of the password should be “LetterLetterNumberNumber.” For example, “HP93.” Once you have generated your password, this should then be entered into your program to decrypt the password. The method of input for the encrypted password is up to
 
you. The second program is a skeleton code to crack the password in regular C without any multithreading syntax. Your task is to use multithreading to split the workload over many threads and find the password. Once the password has been found, the program should finish meaning not all combinations of 2 letters and 2 numbers should be explored unless it’s ZZ99 and the last thread happens to finish last.
Cracks a password using multithreading and dynamic slicing based on thread count (75 marks)
Program finishes appropriately when password has been found (25 marks)

### Explanation:

The following program is OpenMP parallel for password recovery; it brute-forces the recovery of passwords in the format AA00 where A is an uppercase letter and 0 represents a digit. The application sequentially generates all possible password combinations, comparing each to the encrypted password in salt_and_encrypted.

SHA-512 is encrypting the password in plaintext, where an already encrypted value is hardcoded, $6$AS$t7Fo182e7oabr.S5X4hqUZzOQKa/c1a/HfBCklZrwca8Dgx87etXcMX5pBRybxgpY9GO0MN3c7U/s4Q6gFcgc0. Since it involves encryption and extraction of salts, the program captures it from the substr function in order to hash each password guess correctly before its comparison.

The crack function uses three nested loops over two uppercase letters and two digits to generate combinations of passwords. To do this efficiently, OpenMP has been used through the parallel for directive, incorporating collapse to allow more than one iteration to execute in parallel. Mutual exclusion mechanisms are employed to ensure shared data structures, such as count-tracking total number of attempts-and ss-stopping execution once the password is found-are accessed safely between threads.

Each thread encrypts its generated password and checks for a match against the target encrypted password. If it finds a match, it displays the correct password and halts the execution. If no match is found, the program prints a message that the entered password is incorrect.

This program uses OpenMP to parallelize brute-force operations that are computationally intensive, thus ensuring that the system resources are utilized optimally and with precision. With the inclusion of a hardcoded encrypted password, it sets a reference point for processing while tracking the total number of attempted combinations upon completion.

## Output
![image](https://github.com/user-attachments/assets/fa599c04-e1f8-4ba9-966e-a3aca7b9abd3)
![image](https://github.com/user-attachments/assets/4d1238f9-6e90-4280-86d2-8cbd1498b6d8)


## 2.	Matrix Multiplication using multithreading (25% - 100 marks)
You will create a matrix multiplication program which uses multithreading. Matrices are often two-dimensional arrays varying in sizes, for your application, you will only need to multiply two-dimensional ones. Your program will read in the matrices from a supplied file
 
(txt), store them appropriately using dynamic memory allocation features and multiply them by splitting the tasks across “n” threads (any amount of threads). You should use command line arguments to specify which file to read from (argv[1]) and the number of threads to use (argv[2]). If the number of threads requested by the user is greater than the biggest dimension of the matrices to be multiplied, the actual number of threads used in the calculation should be limited to the maximum dimension of the matrices. Your program should be able to take “any” size matrices and multiply them depending on the data found within the file. Some sizes of matrices cannot be multiplied together, for example, if Matrix A is 3x3 and Matrix B is 2x2, you cannot multiply them. If Matrix A is 2x3 and Matrix B is 3x2, then this can be multiplied. You will need to research how to multiply matrices, this will also be covered in the lectures (briefly). If the matrices cannot be multiplied, your program should output an error message notifying the user and move on to the next pair of matrices. Your program should store the results of your successful matrix multiplications in a text file – for every 2 matrices, there should be one resulting matrix. As a minimum, you are expected to use the standard C file handling functions: fopen(), fclose(), fscanf(), and fprintf(), to read and to write your files. stdin and stdout redirection will not be acceptable.
Read data from file appropriately (20 marks)
Using dynamic memory (malloc) for matrix A and matrix B (10 marks) Creating an algorithm to multiply matrices correctly (20 marks)
Using multithreading with equal computations (30 marks)
Storing the correct output matrices in the correct format to a file (20 marks)
 
### Explanation:
This program implements OpenMP parallel matrix multiplication. It optimizes the computations to be executed over multiple matrix pairs by utilizing multi-threading. This code uses a Matrix structure to store necessary information: number of rows, number of columns, and the matrix elements that are dynamically allocated in a two-dimensional array. It reads matrix pairs from an input file, performs multiplication in parallel, and writes results into an output file in quite an efficient way.

The program, at execution, first processes the command-line arguments, where the first argument specifies the number of threads, and the subsequent arguments define the file paths to input matrices. The read_matrix function dynamically loads matrices A, B, and C. By using realloc, it is able to expand memory flexibly and thus can support an undefined number of matrix pairs. For each of them, matrix C is initialized as an empty matrix that will store computed results.

The core computation is performed by the function multiply_matrices, which multiplies rows of matrix A by columns of matrix B, summing the corresponding products to fill in C[i][j]. However, the function contains error-handling mechanisms to prevent invalid operations, such as mismatched dimensions. OpenMP parallelizes the outer loop, enabling multiple matrix pairs to be processed simultaneously, significantly reducing execution time compared to sequential processing.

After the multiplication is performed, results are then written to an output file, say 2331507_matrixresults.txt, implementing the function write_matrix, which nicely formats the output for better readability. At this point, the program frees all the dynamically allocated memory to avoid memory leaks.

This parallelization shows how OpenMP is efficiently used to achieve multi-threaded execution, and because of this, it is very well suited for big dataset processing. This increases performance in matrix computations significantly compared to traditional serial execution.

## Output
![image](https://github.com/user-attachments/assets/1c55535c-d06c-414b-a045-1415190aefb5)
![image](https://github.com/user-attachments/assets/a29809de-4361-44e7-adf0-340eace68c06)


## 3.	Password Cracking using CUDA (30% - 100 marks)
Using a similar concept as question 2, you will now crack passwords using CUDA. As a kernel function cannot use the crypt library, you will be given an encryption function instead which will generate a password for you. Your program will take in an encrypted password and decrypt it using many threads on the GPU. CUDA allows multidimensional thread configurations so your kernel function (which runs on the GPU) will need to be modified according to how you call your function.
Generate encrypted password in the kernel function (using CudaCrypt function) to be compared to original encrypted password (25 marks)
Allocating the correct amount of memory on the GPU based on input data. Memory is freed once used (15 marks)
Program works with multiple blocks and threads – the number of blocks and threads will depend on your kernel function. You will not be penalised if your program only works with a set number of blocks and threads however, your program must use more than one block (axis is up to you) and more than one thread (axis is up to you) (40 marks)
Decrypted password sent back to the CPU and printed (20 marks)

### Explanation:
This is an encryption program that takes a raw password made up of two uppercase letters followed by two digits-for example, XY45-and securely encrypts the password into some form using an encryption algorithm of your own. The actual encryption process is implemented in the function CudaCrypt, which transforms each character with simple arithmetic operations like the addition and subtraction of specific values. It starts the encryption by changing the first character into three ciphertext characters. First, adding 2 and then subtracting 2 and then adding 1 to the ASCII value. Further changes of the same manner are made with other characters so that their encrypted values may also fall within the valid ranges, A-Z or 0-9 through the wraparound rule.

The encryption is done on the GPU through the kernel named encryptPassword, which takes as input the password to be processed in parallel, encrypting it and returning the encrypted output back to the CPU for display. If, for instance, a user enters a password like XY45, a sample output after encryption could be something like ZWCYHE6731, largely increasing the password complexity. The program also implements input validation for the password, which should be in the proper format of two uppercase letters followed by two digits, before attempting to process. This program can guarantee fast and efficient encryption and is scalable because it leverages GPU parallelization. 

### Cracking Section:

The password cracker program is the second stage of the encryption program: it takes an encrypted password as input and, using a brute-force approach, determines the original raw password. This program will use CUDA to generate systematically and test every possible password combination from AA00 to ZZ99.

This approach is performed at the crack kernel, which applies 2D grids of blocks and threads for building all four-character password combinations simultaneously. Each one of these threads generates a specific combination, then encrypts this combination using the CudaCrypt function and tests the encrypted outcome against the encrypted target password, using the gpu_strcmp function. In case of a match, it stores the raw original password in the GPU's memory and copies it into CPU to display it.

For example, if the ciphertext password is ZWCYHE6731, the decryption algorithm goes through its exhaustive permutations until it hits the correct one: XY45. This would be a purely brute-force attempt using CUDA, as thousands and thousands of possible combinations can be tried out simultaneously in parallel. Also, the program has implemented counter logic with atomicAdd to count down the total password combination attempts being tried.

This parallel brute-force decryption utilizes huge parallelism in CUDA for an efficient implementation, ensuring high performance and scaling in cracking encrypted passwords.

## Output
![image](https://github.com/user-attachments/assets/2db1fd91-0ddb-4cfe-ad8e-ceecd5323a77)


## 4. Box Blur using CUDA (30% - 100 marks)
Your program will decode a PNG file into an array and apply the box blur filter. Blurring an image reduces noise by taking the average RGB values around a specific pixel and
setting its RGB to the mean values you’ve just calculated. This smoothens the colour across a matrix of pixels. For this assessment, you will use a 3x3 matrix. For example, if you have a 5x5 image such as the following (be aware that the coordinate values will depend on how you format your 2D array):
 
0,4	1,4	2,4	3,4	4,4
0,3	1,3	2,3	3,3	4,3
0,2	1,2	2,2	3,2	4,2
0,1	1,1	2,1	3,1	4,1
0,0	1,0	2,0	3,0	4,0

The shaded region above represents the pixel we want to blur, in this case, we are focusing on pixel 1,2 (x,y) (Centre of the matrix). To apply the blur for this pixel, you would sum all the Red values from the surrounding coordinates including 1,2 (total of 9 R values) and find the average (divide by 9). This is now the new Red value for coordinate 1,2. You must then repeat this for Green and Blue values. This must be repeated throughout the image. If you are working on a pixel which is not fully surrounded by pixels (8 pixels), you must take the average of however many neighbouring pixels there are.

Your task is to use CUDA to blur an image. Your number of blocks and threads should in an ideal scenario reflect the dimension of the image however, there are limits to the amount of blocks and threads you can spawn in each dimension (regarding block and thread dimensions (x,y,z). You will not be penalised if you do not use different dimensions of blocks and threads, for this assessment, we will accept just one dimensional blocks and threads, e.g. function<<<blockNumber, threadNumber>>>

Reading in an image file into a single or 2D array (5 marks)
Allocating the correct amount of memory on the GPU based on input data. Memory is freed once used (15 marks)
Applying Box filter on image in the kernel function (30 marks) Return blurred image data from the GPU to the CPU (30 marks)
Outputting the correct image with Box Blur applied as a file (20 marks)

### Explanation:
This program utilizes CUDA to parallelize a Box Blur filter on an input PNG image. It smooths the image by reducing the noise and computing the average RGB values of a pixel and those of its neighbors. The image is loaded onto a dynamic 2D array on the CPU through the LodePNG library; fetching image dimensions include width and height. In the following steps, allocate the memory of the target arrays on the GPU using cudaMalloc for the input and output image arrays; then, transfer the information from the CPU to the GPU using cudaMemcpy.

The CUDA kernel box_blur now performs the parallel Box Blur filtering. It assigns one thread to calculate the average RGB values of a pixel and its valid neighbors:. It provides special handling for image boundaries so that the blur extends to the edges and maintains alpha channel values to ensure transparency of the images. Finally, it transfers the processed image data back to the CPU to prevent memory leaks and saves the final blurred image to a new PNG file with a filename provided by the user.

This program efficiently processes image pixels in parallel, using CUDA's capability for parallel computing, by dividing them into grids and blocks: dim3 block_size(16,16);;, while the grid size dynamically changes with respect to the image dimensions in order to process thousands of pixels in one run. This implementation well meets the most important key performance criteria of: reading and writing images efficiently, efficient use of GPU memory, parallelization of Box Blur filtering, efficient data transfer between CPU and GPU, and overall scalability to high-resolution images. With CUDA, this program can keep the computation load high while providing better memory utilization and filtering speed; thus, it can scale well and become effective for any image processing applications.

## Output
![image](https://github.com/user-attachments/assets/430b62d1-8519-48ed-afb8-fe75d54146fc)



