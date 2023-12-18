[1
](https://blog.csdn.net/weixin_43693967/article/details/123719406

编译器管理

# 注释

## Doxygen注释

Doxygen 注释可以包括 `@brief` 标签，还可以包括其他标签，例如 `@param` 用于描述函数参数，`@return` 用于描述返回值等。这种注释方式有助于生成自动化的代码文档，提供给其他开发者阅读并理解代码的功能和使用方式。

举例说明：  

```c++
  /**
   * @brief received point clouds are pushed to #keyframe_queue
   * @param odom_msg
   * @param cloud_msg
   */
  void cloud_callback(const nav_msgs::OdometryConstPtr& odom_msg, const sensor_msgs::PointCloud2::ConstPtr& cloud_msg){...}
```

)https://blog.csdn.net/weixin_43693967/article/details/123719406

编译器管理

# 注释

## Doxygen注释

Doxygen 注释可以包括 `@brief` 标签，还可以包括其他标签，例如 `@param` 用于描述函数参数，`@return` 用于描述返回值等。这种注释方式有助于生成自动化的代码文档，提供给其他开发者阅读并理解代码的功能和使用方式。

举例说明：  

```c++
  /**
   * @brief received point clouds are pushed to #keyframe_queue
   * @param odom_msg
   * @param cloud_msg
   */
  void cloud_callback(const nav_msgs::OdometryConstPtr& odom_msg, const sensor_msgs::PointCloud2::ConstPtr& cloud_msg){...}
```

