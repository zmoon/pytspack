project(
  'tspack',
  'c',
  version : '0.1',
  default_options : ['warning_level=2'],
)

add_languages('fortran')

py_mod = import('python')
py3 = py_mod.find_installation('python')
py3_dep = py3.dependency()
message('py3 path:', py3.path())
message('py3 install dir:', py3.get_install_dir())

ver_numpy = run_command(py3,
  ['-c', 'import numpy; print(numpy.__version__)'],
  check: true,
).stdout().strip()
message('numpy version:', ver_numpy)

incdir_numpy = run_command(py3,
  ['-c', 'import os; os.chdir(".."); import numpy; print(numpy.get_include())'],
  check : true,
).stdout().strip()
message('numpy inc dir:', incdir_numpy)

incdir_f2py = run_command(py3,
    ['-c', 'import os; os.chdir(".."); import numpy.f2py; print(numpy.f2py.get_include())'],
    check : true,
).stdout().strip()
message('f2py inc dir:', incdir_f2py)

inc_np = include_directories(incdir_numpy, incdir_f2py)

ext_f_src = ['pytspack/tspack.f']
ext_module_name = 'tspack'
ext_source = custom_target(
  ext_module_name+'module.c',
  input : ext_f_src,
  output : [ext_module_name+'module.c', ext_module_name+'-f2pywrappers.f'],
  command : [py3, '-m', 'numpy.f2py', '@INPUT@', '-m', ext_module_name, '--lower'],
)

py3.extension_module(
  ext_module_name,
  ext_source,
  incdir_f2py+'/fortranobject.c',
  include_directories: inc_np,
  dependencies : py3_dep,
  install : false,
)
