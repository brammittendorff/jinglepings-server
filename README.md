# Jinglepings Server

## Configuration and Building Instructions

Configuration is managed in `CMakeLists.txt`. This guide walks through setting up dependencies, installing OpenCV with GStreamer support, and building an RTMP server using NGINX with the RTMP module.

### Dependencies

The project has several dependencies:

- A C++17 capable compiler
- CMake
- OpenCV 4 with GStreamer support
- GStreamer with x264, flv, and RTMP support

### Step 1: Install Required Packages on Debian Buster (or Later)

Most dependencies are available directly through Debian's package manager. Install the necessary packages with:

```bash
sudo apt update
sudo apt install -y build-essential cmake gstreamer1.0-plugins-{good,bad,ugly} libpcre3 libpcre3-dev libssl-dev zlib1g-dev unzip
```

### Step 2: Install OpenCV with GStreamer Support

For most applications, you can install OpenCV with GStreamer support directly via `apt`:

```bash
sudo apt install -y libopencv-dev python3-opencv
```

To confirm GStreamer support in the installed OpenCV package, run:

```bash
python3 -c "import cv2; print(cv2.getBuildInformation())" | grep GStreamer
```

If the output shows `GStreamer: YES`, you have GStreamer support enabled.

### Step 3: Compile NGINX with RTMP Module

To enable RTMP streaming, compile NGINX with the RTMP module from source.

1. **Download NGINX and the RTMP Module**:

    ```bash
    cd /usr/local/src
    wget http://nginx.org/download/nginx-1.21.3.tar.gz
    wget https://github.com/arut/nginx-rtmp-module/archive/master.zip
    tar -zxvf nginx-1.21.3.tar.gz
    unzip master.zip
    ```

2. **Compile NGINX with RTMP Support**:

    ```bash
    cd nginx-1.21.3
    ./configure --with-http_ssl_module --add-module=../nginx-rtmp-module-master
    make
    sudo make install
    ```

    This installs NGINX with RTMP support to `/usr/local/nginx`.

3. **Configure NGINX for RTMP Streaming**:

    Open the NGINX configuration file:

    ```bash
    sudo nano /usr/local/nginx/conf/nginx.conf
    ```

    Add the following `rtmp` block **outside** the `http` block:

    ```nginx
    rtmp {
        server {
            listen 1935;

            application live {
                live on;
                record off;
                allow publish all;
                allow play all;
            }
        }
    }
    ```

4. **Start NGINX**:

    ```bash
    sudo /usr/local/nginx/sbin/nginx
    ```

### Step 4: Build the Project

With OpenCV and GStreamer support enabled, you can now build the project:

```bash
mkdir build
cd build
cmake ..
make
```

### Step 5: Configure Socket Capabilities

If raw socket capabilities are needed, run:

```bash
sudo setcap cap_net_raw=+ep jinglepings-server
```

### Step 6: Start the Server

Run the server with:

```bash
./jinglepings-server
```

### Step 7: Stream to the RTMP Server

Your RTMP server is now ready to receive streams at `rtmp://<your-ip>/live`.
