# liboqs Installation and Setup Guide (Kali Linux)

## Objective

Install and configure Open Quantum Safe (liboqs) for Post-Quantum Cryptography experiments.

---

## 1. Check Internet Connectivity

```bash
ping -c 4 8.8.8.8
ping -c 4 github.com
```

If GitHub fails:

```bash
sudo nano /etc/resolv.conf
```

Add:

```text
nameserver 8.8.8.8
nameserver 1.1.1.1
```

---

## 2. Install Dependencies

```bash
sudo apt update

sudo apt install -y git cmake ninja-build build-essential pkg-config libssl-dev curl wget
```

Verify:

```bash
cmake --version
gcc --version
openssl version
```

---

## 3. Create Working Directory

```bash
mkdir -p ~/pqc
cd ~/pqc
```

---

## 4. Clone liboqs

```bash
git clone --recursive https://github.com/open-quantum-safe/liboqs.git
```

Enter directory:

```bash
cd liboqs
```

---

## 5. Build liboqs

Create build directory:

```bash
mkdir build
cd build
```

Configure:

```bash
cmake -GNinja -DOQS_BUILD_ONLY_LIB=ON ..
```

Compile:

```bash
ninja
```

Alternative:

```bash
make -j$(nproc)
```

---

## 6. Install liboqs

Using Ninja:

```bash
sudo ninja install
```

Using Make:

```bash
sudo make install
```

Refresh linker cache:

```bash
sudo ldconfig
```

---

## 7. Verify Installation

Check libraries:

```bash
ls /usr/local/lib | grep oqs
```

Expected:

```text
liboqs.so
liboqs.so.0
```

---

Check CMake package:

```bash
find /usr/local -name "liboqsConfig.cmake"
```

Expected:

```text
/usr/local/lib/cmake/liboqs/liboqsConfig.cmake
```

---

## 8. Configure pkg-config

```bash
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH
```

Permanent:

```bash
echo 'export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH' >> ~/.bashrc
source ~/.bashrc
```

Verify:

```bash
pkg-config --modversion liboqs
```

Expected:

```text
0.x.x
```

---

## 9. Test Library Files

```bash
find /usr/local -name "*oqs*"
```

Expected files:

```text
liboqs.so
liboqs.a
liboqsConfig.cmake
```

---

## 10. Build OQS Provider (Optional)

```bash
cd ~/pqc

git clone --recursive https://github.com/open-quantum-safe/oqs-provider.git

cd oqs-provider
```

Configure:

```bash
cmake -S . -B build -Dliboqs_DIR=/usr/local/lib/cmake/liboqs
```

Build:

```bash
cmake --build build -j$(nproc)
```

Install:

```bash
sudo cmake --install build
```

---

## 11. Verify OQS Provider

```bash
openssl list -providers
```

Expected:

```text
default
oqsprovider
```

---

## 12. Verify PQC Algorithms

```bash
openssl list -kem-algorithms
```

Expected examples:

```text
ML-KEM-512
ML-KEM-768
ML-KEM-1024
```

---

## Troubleshooting

Check if liboqs exists:

```bash
pkg-config --modversion liboqs
```

If not found:

```bash
find /usr/local -name "liboqsConfig.cmake"
```

Check shared library:

```bash
ldconfig -p | grep oqs
```

---

## Final Verification Checklist

✓ Internet Connectivity

✓ Dependencies Installed

✓ liboqs Cloned

✓ liboqs Built

✓ liboqs Installed

✓ pkg-config Detects liboqs

✓ liboqsConfig.cmake Exists

✓ OQS Provider Built (Optional)

✓ OpenSSL Lists oqsprovider

✓ PQC Algorithms Available

---

## Result

Successfully installed and configured liboqs and optional oqs-provider for Post-Quantum Cryptography experimentation on Kali Linux.
