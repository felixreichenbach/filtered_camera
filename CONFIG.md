{
  "processes": [],
  "services": [
    {
      "model": "viam-soleng:ros2:logger",
      "name": "logger",
      "namespace": "viam-soleng",
      "type": "ros2_logger",
      "attributes": {
        "log_level": "debug",
        "ros_topic": "/rosout"
      }
    },
    {
      "attributes": {
        "detect_color": "#ff0000",
        "hue_tolerance_pct": 0.1,
        "segment_size_px": 1000
      },
      "model": "color_detector",
      "name": "colordetector",
      "type": "vision"
    },
    {
      "type": "data_manager",
      "attributes": {
        "capture_dir": "",
        "capture_disabled": false,
        "sync_disabled": false,
        "sync_interval_mins": 0.1,
        "tags": [
          "red"
        ],
        "additional_sync_paths": [
          "/home/ubuntu/rosbag"
        ]
      },
      "name": "data-mgmt",
      "namespace": "rdk"
    }
  ],
  "components": [
    {
      "type": "base",
      "attributes": {
        "publish_rate": "0.2",
        "ros_topic": "/cmd_vel"
      },
      "depends_on": [],
      "model": "viam-soleng:ros2:base",
      "name": "base"
    },
    {
      "service_configs": [
        {
          "type": "data_manager",
          "attributes": {
            "capture_methods": []
          }
        }
      ],
      "name": "imu",
      "type": "movement_sensor",
      "attributes": {
        "ros_topic": "/imu"
      },
      "depends_on": [],
      "model": "viam-soleng:ros2:imu"
    },
    {
      "attributes": {
        "ros_topic": "/oakd/rgb/preview/image_raw"
      },
      "depends_on": [],
      "model": "viam-soleng:ros2:camera",
      "name": "camera",
      "type": "camera"
    },
    {
      "type": "camera",
      "attributes": {
        "pipeline": [
          {
            "attributes": {
              "confidence_threshold": 0.8,
              "detector_name": "colordetector"
            },
            "type": "detections"
          }
        ],
        "source": "camera"
      },
      "depends_on": [],
      "model": "transform",
      "service_configs": [
        {
          "attributes": {
            "capture_methods": []
          },
          "type": "data_manager"
        }
      ],
      "name": "transform",
      "namespace": "rdk"
    },
    {
      "name": "filtered-camera",
      "service_configs": [
        {
          "attributes": {
            "capture_methods": [
              {
                "method": "ReadImage",
                "additional_params": {
                  "mime_type": "image/jpeg"
                },
                "capture_frequency_hz": 1
              }
            ]
          },
          "type": "data_manager"
        }
      ],
      "model": "erh:camera:filtered-camera",
      "type": "camera",
      "namespace": "rdk",
      "attributes": {
        "camera": "camera",
        "vision": "colordetector",
        "window_seconds": 5
      },
      "depends_on": [
        "camera",
        "colordetector"
      ]
    }
  ],
  "fragments": [],
  "modules": [
    {
      "type": "registry",
      "version": "0.0.6",
      "module_id": "viam-soleng:viam-ros2-integration",
      "name": "viam-soleng_viam-ros2-integration"
    },
    {
      "name": "viam-soleng_viam-selective-data-capture",
      "module_id": "viam-soleng:viam-selective-data-capture",
      "version": "0.1.0",
      "type": "registry"
    },
    {
      "version": "0.0.5",
      "type": "registry",
      "name": "erh_filtered-camera",
      "module_id": "erh:filtered-camera"
    }
  ],
  "network": {
    "bind_address": ":8081"
  }
}