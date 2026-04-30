name: Build and Deploy App

on:
  push:
    branches:
      - main

jobs:
  backend-tests:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: 3.9
    - name: Install Dependencies
      run: |
        pip install -r backend/requirements.txt
    - name: Run Tests
      run: python -m unittest backend/tests/test_app.py

  build-apk:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Install Buildozer Dependencies
      run: |
        sudo apt-get update
        sudo apt-get install -y python3-dev openjdk-11-jdk
        pip install buildozer
    - name: Build APK
      run: |
        cd kivy_app
        buildozer android release
    - name: Upload APK
      uses: actions/upload-artifact@v3
      with:
        name: AdamProtocol-APK
        path: kivy_app/bin/*.apk