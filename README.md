## OpenCV C++ Stereo Camera Calibration

This repository contains some sources to calibrate the intrinsics of individual cameras and also the extrinsics of a stereo pair.

### Prerequisites

- Raspberry Pi
- USB Cameras (or you can use Pi's camera module)
- I'm using `Raspbian`. You can use other OS like `Ubuntu Mate for Pi 3` or `Pidora` . Just make sure installation is lightweight and system does not eat up much resources.

### Dependencies

- OpenCV (How to build it from source on Raspberry Pi? Take a look at [this](http://www.pyimagesearch.com/2016/04/18/install-guide-raspberry-pi-3-raspbian-jessie-opencv-3/) detailed blog post on how to)
- popt

### Compilation

Compile all the files using the following commands.

```bash
mkdir build && cd build
cmake ..
make
```

Make sure your are in the `build` folder to run the executables.

### Get images from webcams

This is a small helper tool to grab frames from two webcams operating as a stereo pair.

```bash
./read -w [img_width] -h [img_height] -d [imgs_directory] -e [file_extension]
```

For example, the command for the capturing pairs of images in `calib_imgs/` ( DO NOT FORGET TO CREATE IT FIRST ) would be

```bash
./read -w 640 -h 480 -d "../calib_imgs/" -e "jpg"
```

Images are saved with prefixes `left` and `right` in the desired directory.

### Intrinsic calibration of a single camera

The calibration saves the camera matrix and the distortion coefficients in a YAML file. The datatype for these matrices is `Mat`.

Once you have compiled the sources, run the following command to calibrate the intrinsics.

```bash
./calibrate -w [board_width] -h [board_height] -n [num_imgs] -s [square_size] -d [imgs_directory] -i [imgs_filename] -o [file_extension] -e [output_filename]
```

For example, the command for the test images in `calib_imgs/1/` would be

```bash
./calibrate -w 9 -h 6 -n 27 -s 0.02423 -d "../calib_imgs/" -i "left" -o "cam_left.yml" -e "jpg"
```

### Stereo calibration for extrinisics

Once you have the intrinsics calibrated for both the left and the right cameras, you can use their intrinsics to calibrate the extrinsics between them.

```bash
./calibrate_stereo -n [num_imgs] -u [left_cam_calib] -v [right_cam_calib] -L [left_img_dir] -R [right_img_dir] -l [left_img_prefix] -r [right_img_prefix] -o [output_calib_file]
```

For example, if you calibrated the left and the right cameras using the images in the `calib_imgs/` directory, the following command to compute the extrinsics.

```bash
./calibrate_stereo -n 27 -u cam_left.yml -v cam_right.yml -L ../calib_imgs/ -R ../calib_imgs/ -l left -r right -o cam_stereo.yml
```

### Undistortion and Rectification

Once you have the stereo calibration data, you can remove the distortion and rectify any pair of images so that the resultant epipolar lines become scan lines.

```bash
./undistort_rectify -l [left_img_path] -r [right_img_path] -c [stereo_calib_file] -L [output_left_img] -R [output_right_img]
```

For example

```bash
./undistort_rectify -l ../calib_imgs/left1.jpg -r ../calib_imgs/right1.jpg -c cam_stereo.yml -L left.jpg -R right.jpg
```

## Thanks

Many thanks to [sourishg](https://github.com/sourishg)
