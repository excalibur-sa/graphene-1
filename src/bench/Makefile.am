# preamble
NULL =
AM_CFLAGS =
LDADD =
BUILT_SOURCES =
CLEANFILES =
EXTRA_DIST =
DISTCLEANFILES =
check_PROGRAMS =
noinst_PROGRAMS =
noinst_LTLIBRARIES =
TESTS =

shared_cflags = $(GLIB_CFLAGS)
shared_libadd = $(GLIB_LIBS)

if BUILD_WITH_SSE2
shared_cflags += $(SSE2_CFLAGS)
shared_libadd += $(SSE2_LDFLAGS)
endif

if BUILD_WITH_ARM_NEON
shared_cflags += $(NEON_CFLAGS)
endif

# common library for benchmark utilities
libgraphene_bench_la_CPPFLAGS = -I$(top_srcdir)/src -I$(top_builddir)/src -DG_LOG_DOMAIN=\"Graphene-Bench\"
libgraphene_bench_la_CFLAGS = $(shared_cflags)
libgraphene_bench_la_LIBADD = $(shared_libadd)
libgraphene_bench_la_SOURCES = graphene-bench-utils.h graphene-bench-utils.c
noinst_LTLIBRARIES += libgraphene-bench.la

# common flags for benchmarks
common_cflags = $(shared_cflags) -I$(top_srcdir)/src -I$(top_builddir)/src -DGRAPHENE_COMPILATION=1
common_ldadd = $(shared_libadd) $(top_builddir)/src/libgraphene-1.0.la libgraphene-bench.la

AM_CFLAGS += $(common_cflags)
LDADD += $(common_ldadd)

# scalar is always compiled in
bench_backends = scalar

matrix_scalar_SOURCES = matrix.c
matrix_scalar_CPPFLAGS = -DGRAPHENE_SIMD_BENCHMARK=1 -DGRAPHENE_HAS_SCALAR=1 -DGRAPHENE_SIMD_S=\"scalar\"
noinst_PROGRAMS += matrix-scalar

benchmark-scalar: matrix-scalar$(EXEEXT)
	./matrix-scalar

if BUILD_WITH_SSE2
bench_backends += sse

matrix_sse_SOURCES = matrix.c
matrix_sse_CPPFLAGS = -DGRAPHENE_SIMD_BENCHMARK=1 -DGRAPHENE_HAS_SSE=1 -DGRAPHENE_SIMD_S=\"sse\"
noinst_PROGRAMS += matrix-sse

benchmark-sse: matrix-sse$(EXEEXT)
	./matrix-sse
else
benchmark-sse:
	@echo "*** SSE support: disabled ***"
endif

if BUILD_WITH_GCC
bench_backends += gcc

matrix_gcc_SOURCES = matrix.c
matrix_gcc_CPPFLAGS = -DGRAPHENE_SIMD_BENCHMARK=1 -DGRAPHENE_HAS_GCC=1 -DGRAPHENE_SIMD_S=\"gcc\"
noinst_PROGRAMS += matrix-gcc

benchmark-gcc: matrix-gcc$(EXEEXT)
	./matrix-gcc
else
benchmark-gcc:
	@echo "*** GCC support: disabled ***"
endif

benchmark: Makefile.am
	@$(MAKE) -s benchmark-scalar
	@$(MAKE) -s benchmark-gcc
	@$(MAKE) -s benchmark-sse

.PHONY: benchmark-scalar benchmark-gcc benchmark-sse benchmark
