#
# SPDX-License-Identifier: BSD-3-Clause
#
# Copyright © 2019 Keith Packard
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#
# 1. Redistributions of source code must retain the above copyright
#    notice, this list of conditions and the following disclaimer.
#
# 2. Redistributions in binary form must reproduce the above
#    copyright notice, this list of conditions and the following
#    disclaimer in the documentation and/or other materials provided
#    with the distribution.
#
# 3. Neither the name of the copyright holder nor the names of its
#    contributors may be used to endorse or promote products derived
#    from this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
# "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
# LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
# FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE
# COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT,
# INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
# (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
# SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
# HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT,
# STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
# ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED
# OF THE POSSIBILITY OF SUCH DAMAGE.
#
libm_machine_dirs = {
		      'aarch64' : 'aarch64',
		      'arm' : 'arm',
		      'i386' : 'i386',
		      'i486' : 'i386',
		      'i586' : 'i386',
		      'i686' : 'i386',
		      'nds32' : 'nds32',
		      'riscv' : 'riscv',
		      'riscv32' : 'riscv',
		      'riscv64' : 'riscv',
		      'spu' : 'spu',
		      'x86' : 'i386',
		      'x86_64' : 'x86_64',
		      'xtensa': 'xtensa',
		    }

libdirs = ['math', 'common', 'complex']

libnames = libdirs

need_generic_fenv = true

if libm_machine_dirs.has_key(host_cpu_family)
  libm_machine_dir = join_paths('machine', libm_machine_dirs[host_cpu_family])
  libdirs = [libm_machine_dir] + libdirs
  libnames = ['machine'] + libnames
  if run_command('ls', join_paths(libm_machine_dir, 'fegetenv.c')).returncode() == 0 or run_command('ls', join_paths(libm_machine_dir, 'fenv.c')).returncode() == 0 
    need_generic_fenv = false
  endif
else
  warning('No libm machine directory for CPU family ' + host_cpu_family)
endif

if need_generic_fenv
  libdirs = ['fenv'] + libdirs
  libnames = ['fenv'] + libnames
endif

math_inc = [include_directories('common'), inc]

foreach libdir : libdirs
  subdir(libdir)
endforeach

foreach target : targets
  value = get_variable('target_' + target)
  libobjs = []

  foreach libname : libnames
    libobjs += get_variable('lib_' + libname + target).extract_all_objects(recursive:true)
  endforeach

  instdir = join_paths(get_option('libdir'), value[0])

  if target == ''
    libm_name = 'm'
  else
    libm_name = join_paths(target, 'libm')
  endif

  set_variable('lib_m' + target,
	       static_library(libm_name,
			      install : true,
			      install_dir : instdir,
			      pic: false,
			      objects : libobjs))
endforeach

if enable_tests
  subdir('test')
endif

