cmake_minimum_required(VERSION 2.8.3)
project(dwa_local_planner_constraint)

find_package(catkin REQUIRED
        COMPONENTS
            base_local_planner
            cmake_modules
            costmap_2d
            dynamic_reconfigure
            nav_core
            nav_msgs
            pluginlib
            pcl_conversions
            roscpp
            tf
        )

find_package(Eigen REQUIRED)
find_package(PCL REQUIRED)
find_package(base_local_planner REQUIRED) # not required because the find_package ... REQUIRED....?


include_directories(
    include
    ${catkin_INCLUDE_DIRS}
    ${EIGEN_INCLUDE_DIRS}
    ${PCL_INCLUDE_DIRS}
    ${base_local_planner_INCLUDE_DIRS}
    )
add_definitions(${EIGEN_DEFINITIONS})

link_directories(${catkin_LIBRARY_DIRS})

# dynamic reconfigure
generate_dynamic_reconfigure_options(
    cfg/DWAPlanner.cfg
)

catkin_package(
    INCLUDE_DIRS include
    LIBRARIES dwa_local_planner_constraint
    CATKIN_DEPENDS
        dynamic_reconfigure
        pluginlib
        roscpp
)

add_library(
    dwa_local_planner_constraint src/velocity_costmaps_cost_function.cpp  src/dwa_planner.cpp src/dwa_planner_ros.cpp
    )


target_link_libraries(dwa_local_planner_constraint ${catkin_LIBRARIES})
add_dependencies(dwa_local_planner_constraint dwa_local_planner_constraint_gencfg)
add_dependencies(dwa_local_planner_constraint nav_msgs_gencpp)

install(TARGETS dwa_local_planner_constraint
       ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
       LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
       )

install(FILES blp_plugin.xml
    DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  PATTERN ".svn" EXCLUDE
)
