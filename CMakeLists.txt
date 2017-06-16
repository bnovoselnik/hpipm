###################################################################################################
#                                                                                                 #
# This file is part of HPIPM.                                                                     #
#                                                                                                 #
# HPIPM -- High Performance Interior Point Method.                                                #
# Copyright (C) 2017 by Gianluca Frison.                                                          #
# Developed at IMTEK (University of Freiburg) under the supervision of Moritz Diehl.              #
# All rights reserved.                                                                            #
#                                                                                                 #
# HPMPC is free software; you can redistribute it and/or                                          #
# modify it under the terms of the GNU Lesser General Public                                      #
# License as published by the Free Software Foundation; either                                    #
# version 2.1 of the License, or (at your option) any later version.                              #
#                                                                                                 #
# HPMPC is distributed in the hope that it will be useful,                                        #
# but WITHOUT ANY WARRANTY; without even the implied warranty of                                  #
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.                                            #
# See the GNU Lesser General Public License for more details.                                     #
#                                                                                                 #
# You should have received a copy of the GNU Lesser General Public                                #
# License along with HPMPC; if not, write to the Free Software                                    #
# Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301  USA                  #
#                                                                                                 #
# Author: Gianluca Frison, gianluca.frison (at) imtek.uni-freiburg.de                             #
#                                                                                                 #
###################################################################################################

cmake_minimum_required(VERSION 2.8.11)
project(hpipm)

# Target architecture
set(TARGET GENERIC)

# BLAS and LAPACK version (for LA=BLAS in BLASFEO)
set(REF_BLAS 0)
#set(REF_BLAS OPENBLAS)
#set(REF_BLAS NETLIB)
#set(REF_BLAS MKL)
#set(REF_BLAS BLIS)
#set(REF_BLAS ATLAS)

# operating system
#set(OS LINUX)
#set(OS MAC)
#set(OS WINDOWS)

# C Compiler
set(CC_COMPILER gcc)
#set(CC_COMPILER clang)
#set(CC_COMPILER x86_64-w64-mingw32-gcc)

# installation directory
set(PREFIX /opt)

# BLASFEO installation directory
set(BLASFEO_PATH /opt/blasfeo)

# enable runtine checks
set(RUNTIME_CHECKS 0)
#set(RUNTIME_CHECKS 0)

# compiler flags
set(CMAKE_C_FLAGS "")

# optimization flags
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O2 -fPIC")

# debugging flags
#set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g")

# search directories
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -I${BLASFEO_PATH}/include")

#
if(${REF_BLAS} EQUAL 0)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ")
endif(${REF_BLAS} EQUAL 0)
if(${REF_BLAS} EQUAL OPENBLAS)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DREF_BLAS_OPENBLAS -I/opt/openblas/include")
endif(${REF_BLAS} EQUAL OPENBLAS)
if(${REF_BLAS} EQUAL BLIS)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DREF_BLAS_BLIS -std=c99")
endif(${REF_BLAS} EQUAL BLIS)
if(${REF_BLAS} EQUAL NETLIB)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DREF_BLAS_NETLIB")
endif(${REF_BLAS} EQUAL NETLIB)
if(${REF_BLAS} EQUAL MKL)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DREF_BLAS_MKL -m64 -I/opt/intel/mkl/include")
endif(${REF_BLAS} EQUAL MKL)
if(${REF_BLAS} EQUAL ATLAS)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DREF_BLAS_ATLAS")
endif(${REF_BLAS} EQUAL ATLAS)

#
if(${RUNTIME_CHECKS} EQUAL 1)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DRUNTIME_CHECKS")
endif(${RUNTIME_CHECKS} EQUAL 1)

# architecture-specific flags
if(${TARGET} EQUAL GENERIC)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DTARGET_GENERIC")
endif(${TARGET} EQUAL GENERIC)



# source files
# dense qp
file(GLOB DENSE_QP_SRC
	${PROJECT_SOURCE_DIR}/dense_qp/d_dense_qp.c
	${PROJECT_SOURCE_DIR}/dense_qp/d_dense_qp_sol.c
	${PROJECT_SOURCE_DIR}/dense_qp/d_dense_qp_kkt.c
	${PROJECT_SOURCE_DIR}/dense_qp/d_dense_qp_ipm_hard.c
	${PROJECT_SOURCE_DIR}/dense_qp/s_dense_qp.c
	${PROJECT_SOURCE_DIR}/dense_qp/s_dense_qp_sol.c
	)
# ocp qp
file(GLOB OCP_QP_SRC
	${PROJECT_SOURCE_DIR}/ocp_qp/d_ocp_qp.c
	${PROJECT_SOURCE_DIR}/ocp_qp/d_ocp_qp_sol.c
	${PROJECT_SOURCE_DIR}/ocp_qp/d_ocp_qp_kkt.c
	${PROJECT_SOURCE_DIR}/ocp_qp/d_ocp_qp_ipm_hard.c
	${PROJECT_SOURCE_DIR}/ocp_qp/s_ocp_qp.c
	${PROJECT_SOURCE_DIR}/ocp_qp/s_ocp_qp_sol.c
	)
# core qp
file(GLOB CORE_QP_SRC
	${PROJECT_SOURCE_DIR}/core_qp/d_core_qp_ipm_hard.c
	${PROJECT_SOURCE_DIR}/core_qp/d_core_qp_ipm_hard_aux.c
	)
# cond
file(GLOB COND_SRC
	${PROJECT_SOURCE_DIR}/cond/d_cond.c
	${PROJECT_SOURCE_DIR}/cond/d_cond_aux.c
	)

set(HPIPM_SRC ${DENSE_QP_SRC} ${OCP_QP_SRC} ${CORE_QP_SRC} ${COND_SRC})

# add library
add_library(hpipm ${HPIPM_SRC})
#add_library(hpipm SHARED ${HPIPM_SRC})
#target_link_libraries(hpipm)

# test problems
add_subdirectory(test_problems)