# Heap
Heap is the memory space that can be divided to chunk size. and it can be allocated by calls. Each heap 
should be belonged to only one arena.
# Chunk
Chunk is the place that actually allocated by malloc() fuction in C language. When it's allocated, it has
8 X n bytes size(n is natural number in here). chunk-oriented mallocs divide big heap into variable size chunk and allocate
it. One chunk is in only one heap and it belongs to only one arena(because heap is belonged to one arena).
* When chunk get free, it is added to arena-related information(which is linked list) to use again. 
* chunk is made up with prev_size,size,AMP,ayload,size and AMP again
* prev_size is pointed with mchunkptr, and you can think that chunk is starts from prev_size. 
* AMP flag each appears A= Allocated Arena ,M=Mmap'd ,P=prev in use   
for example if prev chunk is used, P=1 
* return address of malloc is not starting point of chunk(prev_size) but payload. So it can be used right away after using pointer
# Arena
Arena is the referece of one or many heaps , of structure that contains linked list of heap chunks.
* arena is divided to main_arena and other arenas
* arenas except main_arena has next pointer which points additional arenas
* one of the arena has pointer which points <top> chunk
* <top> chunk is the chunk which is not yet allocated
* main_arena does not have heap info(ar_ptr,prev,next,size...) and it even does not exist in heap but in data segment
# Bins
Arena has list of pointer that points at free'd chunk to reallocate free'd chunks fast. These list are called
"bins".
## fastBin
FastBin means small chunks under certain size and they are linked by linked list. It has LIFO structure. Though fragmentation increase by using fastbin,
it can manage call of same size allocation fast. 
* mallopt(M_MXFAST,value); can set size limit. We can set until 0-80*sizeof(size_t)/4. And when size is 0, 
the fast bins are inactivated.

