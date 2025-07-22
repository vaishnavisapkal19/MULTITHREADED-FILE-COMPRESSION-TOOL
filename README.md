# MULTITHREADED-FILE-COMPRESSION-TOOL

*COMPANY*: CODTECH IT SOLUTIONS 

*NAME*: SAPKAL VAISHNAVI GANESH 

*INTERN ID*: CTO8DG447

*DOMAIN*:C++

*DURATION*:8 WEEKS

*MENTOR*:NEELA SANTOSH 

*DESCRIPTION*: The given C++ program demonstrates a multithreaded file compression and decompression system using the zlib library. It reads a file in chunks, compresses each chunk in parallel using threads, writes the compressed data to a new file, then decompresses it back into its original form, and saves it to another file. This program showcases concepts such as file I/O, compression with zlib, multithreading using std::thread, and performance measurement using std::chrono.

🔹 Libraries Used
iostream, fstream: For console and file I/O.

vector, string: For managing data and file chunks.

thread, mutex, chrono: For multithreading and timing.

zlib.h: For compression and decompression operations using the zlib library.

🔹 Key Components
1. CHUNK_SIZE and Chunk Structure
The constant CHUNK_SIZE is set to 1 MB. Files are split into these chunk sizes for individual compression and decompression. The Chunk struct holds:

input: Original data.

output: Compressed or decompressed data.

compressed_size: Size after compression.

🔹 Compression and Decompression Functions
compress_chunk(Chunk&): Compresses the input data using zlib::compress(), stores result in output, and records the size.

decompress_chunk(Chunk&, size_t): Restores original data using zlib::uncompress().

Both functions handle errors if compression/decompression fails.

🔹 File Reading/Writing
read_file_chunks(): Reads the input file in CHUNK_SIZE blocks and stores them in Chunk objects.

write_compressed_file(): Writes the compressed chunks to a file with a 4-byte size header before each chunk.

read_compressed_file(): Reads the compressed file, interpreting chunk sizes and storing compressed data accordingly.

write_decompressed_file(): Writes the decompressed output data into a final file.

🔹 Multithreading
The function run_multithreaded() accepts a vector of chunks and a processing function (either compression or decompression). It spawns a thread for each chunk, applying the function in parallel. This approach significantly boosts performance for large files by utilizing multi-core processors.

🔹 Main Function Flow
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

🔹 Conclusion
This program is an efficient and well-structured example of modern C++ programming. It combines file chunking, parallel processing, and data compression to demonstrate how performance-intensive tasks like compression can be scaled using threads. It’s ideal for handling large files and shows practical applications of zlib and multithreading in real-world file systems. 
FOR SOME REASION I COULD NOT POST THE SCREEN SHORT OF THE OUT NUT HERE IS MY CODE: 

#include <iostream>
#include <fstream>
#include <vector>
#include <thread>
#include <chrono>
#include <zlib.h>
#include <mutex>

constexpr size_t CHUNK_SIZE = 1024 * 1024;

std::mutex io_mutex;

struct Chunk {
    std::vector<unsigned char> input;
    std::vector<unsigned char> output;
    size_t compressed_size;
};


void compress_chunk(Chunk& chunk) {
    uLongf destLen = compressBound(chunk.input.size());
    chunk.output.resize(destLen);

    int res = compress(chunk.output.data(), &destLen, chunk.input.data(), chunk.input.size());
    if (res != Z_OK) {
        std::cerr << "Compression failed with error: " << res << std::endl;
        return;
    }

    chunk.output.resize(destLen);
    chunk.compressed_size = destLen;
}


void decompress_chunk(Chunk& chunk, size_t original_size) {
    chunk.output.resize(original_size);

    uLongf destLen = original_size;
    int res = uncompress(chunk.output.data(), &destLen, chunk.input.data(), chunk.input.size());

    if (res != Z_OK) {
        std::cerr << "Decompression failed with error: " << res << std::endl;
        return;
    }
}

void read_file_chunks(const std::string& filename, std::vector<Chunk>& chunks) {
    std::ifstream file(filename, std::ios::binary);

    while (!file.eof()) {
        Chunk chunk;
        chunk.input.resize(CHUNK_SIZE);
        file.read(reinterpret_cast<char*>(chunk.input.data()), CHUNK_SIZE);
        chunk.input.resize(file.gcount());

        if (!chunk.input.empty())
            chunks.push_back(std::move(chunk));
    }
}

void write_compressed_file(const std::string& filename, const std::vector<Chunk>& chunks) {
    std::ofstream file(filename, std::ios::binary);

    for (const auto& chunk : chunks) {
        uint32_t size = static_cast<uint32_t>(chunk.compressed_size);
        file.write(reinterpret_cast<const char*>(&size), sizeof(size));
        file.write(reinterpret_cast<const char*>(chunk.output.data()), chunk.compressed_size);
    }
}

void read_compressed_file(const std::string& filename, std::vector<Chunk>& chunks) {
    std::ifstream file(filename, std::ios::binary);

    while (file.peek() != EOF) {
        uint32_t size;
        file.read(reinterpret_cast<char*>(&size), sizeof(size));
        Chunk chunk;
        chunk.input.resize(size);
        file.read(reinterpret_cast<char*>(chunk.input.data()), size);
        chunks.push_back(std::move(chunk));
    }
}

void write_decompressed_file(const std::string& filename, const std::vector<Chunk>& chunks) {
    std::ofstream file(filename, std::ios::binary);

    for (const auto& chunk : chunks) {
        file.write(reinterpret_cast<const char*>(chunk.output.data()), chunk.output.size());
    }
}

template<typename Func>
void run_multithreaded(std::vector<Chunk>& chunks, Func func, size_t original_chunk_size = 0) {
    std::vector<std::thread> threads;
    for (auto& chunk : chunks) {
        threads.emplace_back([&chunk, func, original_chunk_size]() {
            func(chunk, original_chunk_size);
        });
    }

    for (auto& t : threads) {
        t.join();
    }
}

int main() {
    std::string input_file = "testfile.txt";
    std::string compressed_file = "testfile.z";
    std::string decompressed_file = "testfile_out.txt";

    
    std::vector<Chunk> chunks;
    read_file_chunks(input_file, chunks);

    
    auto start = std::chrono::high_resolution_clock::now();
    run_multithreaded(chunks, [](Chunk& c, size_t) { compress_chunk(c); });
    auto end = std::chrono::high_resolution_clock::now();

    std::cout << "Compression Time: "
              << std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count()
              << " ms\n";

    write_compressed_file(compressed_file, chunks);

    
    std::vector<Chunk> compressed_chunks;
    read_compressed_file(compressed_file, compressed_chunks);

    
    start = std::chrono::high_resolution_clock::now();
    run_multithreaded(compressed_chunks, [](Chunk& c, size_t s) { decompress_chunk(c, s); }, CHUNK_SIZE);
    end = std::chrono::high_resolution_clock::now();

    std::cout << "Decompression Time: "
              << std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count()
              << " ms\n";

    write_decompressed_file(decompressed_file, compressed_chunks);

    std::cout << "Done.\n";
    return 0;
}
