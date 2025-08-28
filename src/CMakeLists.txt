esma_add_subdirectory (Shared)

# Instead of just add_subdirectory because ioda, say, depends on
# oops to be built *AND INSTALLED*, we need to use ExternalProject to
# ensure that the dependencies are built in the right order.

include(ExternalProject)

ExternalProject_Add(
  crtm
  SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/@crtm
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_INSTALL_PREFIX}
)

ExternalProject_Add(
  oops
  SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/@oops
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_INSTALL_PREFIX}
)


ExternalProject_Add(
  ioda
  SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/@ioda
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_INSTALL_PREFIX}
    -DENABLE_IODA_DATA:BOOL=ON
)
ExternalProject_Add_StepDependencies(ioda build oops)


ExternalProject_Add(
  ufo
  SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/@ufo
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_INSTALL_PREFIX}
    -DENABLE_UFO_DATA:BOOL=ON
)
ExternalProject_Add_StepDependencies(ufo build ioda)
ExternalProject_Add_StepDependencies(ufo build oops)
ExternalProject_Add_StepDependencies(ufo build crtm)


ExternalProject_Add(
  vader
  SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/@vader
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_INSTALL_PREFIX}
    -DENABLE_IODA_DATA:BOOL=ON
)
ExternalProject_Add_StepDependencies(vader build oops)

ExternalProject_Add(
  saber
  SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/@saber
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_INSTALL_PREFIX}
    -DENABLE_IODA_DATA:BOOL=ON
)
ExternalProject_Add_StepDependencies(saber build vader)


ExternalProject_Add(
  fv3-jedi
  SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/@fv3-jedi
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_INSTALL_PREFIX}
    -DFV3_FORECAST_MODEL=GEOS
    -DFV3_FORECAST_MODEL_ROOT=/discover/nobackup/projects/gmao/SIteam/Models/GEOSgcm-v11.7.0/install-spack-stack-1.9-oneapi
    )
ExternalProject_Add_StepDependencies(fv3-jedi build oops)
ExternalProject_Add_StepDependencies(fv3-jedi build saber)
ExternalProject_Add_StepDependencies(fv3-jedi build vader)
ExternalProject_Add_StepDependencies(fv3-jedi build ioda)
ExternalProject_Add_StepDependencies(fv3-jedi build crtm)
ExternalProject_Add_StepDependencies(fv3-jedi build ufo)

file (GLOB obsimul_files gaan/*.py)
install (
  FILES ${gaan_files}
  DESTINATION lib/Python/gaan/
  )

