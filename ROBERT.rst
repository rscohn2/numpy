=====
 GCC
=====

gcc vectorization
=================

gcc -S -mavx2 -fopt-info-vec-all -ftree-vectorize -fopt-info-vec-missed -ftree-vectorizer-verbose=5 \
-g -O2 -c maskput.c

O3 will turn on -ftree-vectorize

auto vec description_

.. _description: https://gcc.gnu.org/projects/tree-ssa/vectorization.html

ICS Setup for Linux
===================

::

   sudo mount -t drvfs \\hdcfsv02a-cifs.hd.intel.com\wref1 /mnt/rdrive
   


putmask

numpy
=====



::

   git clone https://github.com/rscohn2/numpy.git
   source ~/local/venv/numpy/bin/activate
   pip install -r test_requirements.txt
   pip install asv
   
   python runtests.py --bench bench_itemselection.PutMask.time_dense
ls numpy/core/src/multiarray/item_selection.c 

innner loop:

        char *tmp_src = src;
        for (npy_intp i = 0, j = 0; i < ni; i++, j++) {
            if (NPY_UNLIKELY(j >= nv)) {
                j = 0;
                tmp_src = src;
            }
            if (mask_data[i]) {
                memmove(dest, tmp_src, chunk);
            }
            dest += chunk;
            tmp_src += chunk;
        }

::

   vtune -collect-with runsa -knob enable-user-tasks=true -knob event-config=INST_RETIRED.ANY:sa=1900000,CPU_CLK_UNHALTED.THREAD:sa=2000003,MEM_INST_RETIRED.ALL_LOADS:sa=2000003,MEM_INST_RETIRED.ALL_STORES_PS:sa=2000003

   gcc -DNPY_INTERNAL_BUILD=1 -DHAVE_NPY_CONFIG_H=1 -D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE=1 -D_LARGEFILE64_SOURCE=1 -DHAVE_CBLAS -Ibuild/src.linux-x86_64-3.8/numpy/core/src/common -Ibuild/src.linux-x86_64-3.8/numpy/core/src/umath -Inumpy/core/include -Ibuild/src.linux-x86_64-3.8/numpy/core/include/numpy -Ibuild/src.linux-x86_64-3.8/numpy/distutils/include -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -Inumpy/core/src/_simd -I/home/rscohn2/venv/numpy/include -I/usr/include/python3.8 -Ibuild/src.linux-x86_64-3.8/numpy/core/src/common -Ibuild/src.linux-x86_64-3.8/numpy/core/src/npymath -c -mavx2 -O3 -ftree-vectorizer-verbose=5 -fopt-info-vec-all
