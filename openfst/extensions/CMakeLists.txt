# Copyright 2026 The OpenFst Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

if(OPENFST_ENABLE_CATEGORIAL)
  add_subdirectory(categorial)
endif()

if(OPENFST_ENABLE_COMPACT_FSTS)
  add_subdirectory(compact)
endif()

if(OPENFST_ENABLE_COMPRESS)
  add_subdirectory(compress)
endif()

if(OPENFST_ENABLE_CONST_FSTS)
  add_subdirectory(const)
endif()

if(OPENFST_ENABLE_FAR)
  add_subdirectory(far)
endif()

if(OPENFST_ENABLE_LINEAR_FSTS)
  add_subdirectory(linear)
endif()

if(OPENFST_ENABLE_LOOKAHEAD_FSTS)
  add_subdirectory(lookahead)
endif()

if(OPENFST_ENABLE_NGRAM_FSTS)
  add_subdirectory(ngram)
endif()

# PDT must come before MPDT as MPDT depends on it
if(OPENFST_ENABLE_PDT)
  add_subdirectory(pdt)
endif()

if(OPENFST_ENABLE_MPDT)
  add_subdirectory(mpdt)
endif()

if(OPENFST_ENABLE_PYTHON)
  add_subdirectory(python)
endif()

if(OPENFST_ENABLE_SPECIAL)
  add_subdirectory(special)
endif()
