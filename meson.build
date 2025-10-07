project('gsm', 'c',
  version: '1.0',
  default_options: ['c_std=c89', 'warning_level=2', 'optimization=2']
)

# Configuration
cc = meson.get_compiler('c')

# Compile flags
add_project_arguments('-DNeedFunctionPrototypes=1', language: 'c')
add_project_arguments('-Wno-comment', language: 'c')

# Optional features
sasr = get_option('sasr')
wav49 = get_option('wav49')
ltp_cut = get_option('ltp_cut')

if sasr
  add_project_arguments('-DSASR', language: 'c')
endif

if wav49
  add_project_arguments('-DWAV49', language: 'c')
endif

if ltp_cut
  add_project_arguments('-DLTP_CUT', language: 'c')
endif

# Include directories
inc_dir = include_directories('inc')

# GSM library sources
gsm_sources = files(
  'src/add.c',
  'src/code.c',
  'src/debug.c',
  'src/decode.c',
  'src/long_term.c',
  'src/lpc.c',
  'src/preprocess.c',
  'src/rpe.c',
  'src/gsm_destroy.c',
  'src/gsm_decode.c',
  'src/gsm_encode.c',
  'src/gsm_explode.c',
  'src/gsm_implode.c',
  'src/gsm_create.c',
  'src/gsm_print.c',
  'src/gsm_option.c',
  'src/short_term.c',
  'src/table.c'
)

# Build GSM library
libgsm = static_library('gsm',
  gsm_sources,
  include_directories: inc_dir,
  install: true
)

# Toast application sources
toast_sources = files(
  'src/toast.c',
  'src/toast_lin.c',
  'src/toast_ulaw.c',
  'src/toast_alaw.c',
  'src/toast_audio.c'
)

# Build toast executable
toast_exe = executable('toast',
  toast_sources,
  include_directories: inc_dir,
  link_with: libgsm,
  install: true
)

# Create symlinks for untoast and tcat
meson.add_install_script('sh', '-c',
  'ln -sf toast "$MESON_INSTALL_DESTDIR_PREFIX/@0@/untoast"'.format(get_option('bindir'))
)
meson.add_install_script('sh', '-c',
  'ln -sf toast "$MESON_INSTALL_DESTDIR_PREFIX/@0@/tcat"'.format(get_option('bindir'))
)

# Install headers
install_headers('inc/gsm.h')

# Install man pages
install_man(
  'man/gsm.3',
  'man/gsm_explode.3',
  'man/gsm_option.3',
  'man/gsm_print.3',
  'man/toast.1'
)

# Test executables (optional)
if get_option('build_tests')
  test_inc = include_directories('inc', 'tls')
  
  # Taste library for tests
  taste_lib = static_library('taste',
    'tls/taste.c',
    include_directories: test_inc
  )
  
  # TLS test programs
  foreach prog : ['sweet', 'bitter', 'ginger', 'sour']
    executable(prog,
      'tls/@0@.c'.format(prog),
      include_directories: test_inc,
      link_with: taste_lib,
      build_by_default: false
    )
  endforeach
  
  # TST test programs
  foreach prog : ['lin2cod', 'cod2lin', 'lin2txt', 'cod2txt', 'gsm2cod']
    executable(prog,
      'tst/@0@.c'.format(prog),
      include_directories: inc_dir,
      link_with: libgsm,
      build_by_default: false
    )
  endforeach
  
  # Add test
  add_test_exe = executable('add_test',
    'add-test/add_test.c',
    build_by_default: false
  )
  
  test('add_test', add_test_exe,
    args: ['<', files('add-test/add_test.dta')],
    is_parallel: false
  )
endif

# pkg-config file
pkg = import('pkgconfig')
pkg.generate(libgsm,
  description: 'GSM 06.10 lossy speech compression library',
  name: 'gsm'
)
