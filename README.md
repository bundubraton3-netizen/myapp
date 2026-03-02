name: Build APK

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Install system dependencies
      run: |
        sudo apt update
        sudo apt install -y python3-pip python3-setuptools git zip unzip openjdk-17-jdk zlib1g-dev

    - name: Install Buildozer
      run: |
        pip install buildozer cython

    - name: Create buildozer.spec
      run: |
        buildozer init
        sed -i "s/^title = .*/title = MyApplication/" buildozer.spec
        sed -i "s/^requirements = .*/requirements = python3,kivy/" buildozer.spec

    - name: Build APK
      run: |
        buildozer -v android debug

    - name: Upload APK
      uses: actions/upload-artifact@v3
      with:
        name: apk
        path: bin/*.apk
