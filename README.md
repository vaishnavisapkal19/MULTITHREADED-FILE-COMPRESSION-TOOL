# MULTITHREADED-FILE-COMPRESSION-TOOL

*COMPANY*: CODTECH IT SOLUTIONS 

*NAME*: SAPKAL VAISHNAVI GANESH 

*INTERN ID*: CTO8DG447

*DOMAIN*:C++

*DURATION*:8 WEEKS

*MENTOR*:NEELA SANTOSH 

*DESCRIPTION*: The given C++ program demonstrates a multithreaded file compression and decompression system using the zlib library. It reads a file in chunks, compresses each chunk in parallel using threads, writes the compressed data to a new file, then decompresses it back into its original form, and saves it to another file. This program showcases concepts such as file I/O, compression with zlib, multithreading using std::thread, and performance measurement using std::chrono.
Libraries Used
iostream, fstream: For console and file I/O.
vector, string: For managing data and file chunks.
thread, mutex, chrono: For multithreading and timing.
zlib.h: For compression and decompression operations using the zlib library.
1. CHUNK_SIZE and Chunk Structure
The constant CHUNK_SIZE is set to 1 MB. Files are split into these chunk sizes for individual compression and decompression. The Chunk struct holds:
input: Original data.
output: Compressed or decompressed data.
compressed_size: Size after compression.
Compression and Decompression Functions
compress_chunk(Chunk&): Compresses the input data using zlib::compress(), stores result in output, and records the size.
decompress_chunk(Chunk&, size_t): Restores original data using zlib::uncompress().
Both functions handle errors if compression/decompression fails.
File Reading/Writing
read_file_chunks(): Reads the input file in CHUNK_SIZE blocks and stores them in Chunk objects.
write_compressed_file(): Writes the compressed chunks to a file with a 4-byte size header before each chunk.
read_compressed_file(): Reads the compressed file, interpreting chunk sizes and storing compressed data accordingly.
write_decompressed_file(): Writes the decompressed output data into a final file.
Multithreading
The function run_multithreaded() accepts a vector of chunks and a processing function (either compression or decompression). It spawns a thread for each chunk, applying the function in parallel. This approach significantly boosts performance for large files by utilizing multi-core processors.
Main Function Flow
Initialize Filenames:
testfile.txt: Original input.
testfile.z: Compressed output.
testfile_out.txt: Final decompressed file.
Read Input File:
Split into 1MB chunks using read_file_chunks().
Compress Chunks:
Time the compression process using chrono.
Call run_multithreaded() with the compress_chunk() function.
Write Compressed File:
Save all compressed chunks into testfile.z.
Read and Decompress:
Load the compressed file using read_compressed_file().
Decompress with run_multithreaded() using decompress_chunk().
Write Final Output:
Save decompressed data to testfile_out.txt.
Display Timing:
Compression and decompression times are shown in milliseconds.
Conclusion
This program is an efficient and well-structured example of modern C++ programming. It combines file chunking, parallel processing, and data compression to demonstrate how performance-intensive tasks like compression can be scaled using threads. It’s ideal for handling large files and shows practical applications of zlib and multithreading in real-world file systems. 
FOR SOME REASION I COULD NOT POST THE SCREEN SHORT OF THE OUT
