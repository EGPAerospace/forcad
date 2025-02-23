# 1DPerformance library
SET(FORCAD_src ${SRC}/forcad_nurbs_curve.f90
            ${SRC}/forcad_nurbs_surface.f90
            ${SRC}/forcad_nurbs_volume.f90
            ${SRC}/forcad_utils.f90
            ${SRC}/forcad.f90
)

ADD_LIBRARY(${FORCADLIB} STATIC ${FORCAD_src})