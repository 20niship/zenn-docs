---
title: "【C++】Tips集"
emoji: ""
type: ""
topics: []
published: false
---

* [変数名とその値を標準出力](#変数名とその値を標準出力)
* [ベクトルたち](#ベクトルたち)
* [std::vector自作](#stdvector自作)
* [WinMainのプログラムでもコンソール出力を可能にする](#WinMainのプログラムでもコンソール出力を可能にする)
* [関数マクロ](#関数マクロ)
* [スピーカーの音量変更](#スピーカーの音量変更)
* [マイクの入力レベル変更](#マイクの入力レベル変更)
  
  
### 変数名とその値を標準出力

define disp(A) (std::cout << #A << " = " << A << "\n")

### ベクトルたち

struct Vector2f {
  float x, y;
  bool valid = true;
  Vector2f() { reset(); }
  Vector2f(float x_, float y_) { x = x_; y = y_; valid = true; }
  void reset() { x = 0.0f; y = 0.0f; }
  float operator[](int i) {if (i == 0) { return x; }else{ return y; }}
  Vector2f operator+=(const Vector2f& other) { x += other.x; y += other.y; return *this; }
  Vector2f operator*=(const float& mul) { x = x * mul; y = y * mul; return *this; }
  Vector2f operator/=(const float& div) { x = x / div; y = y / div; return *this; }
  Vector2f operator-(const Vector2f& other) { x -= other.x; y -= other.y; return *this; }
  Vector2f operator+(const Vector2f& other) { return Vector2f(x + other.x, y + other.y); }
};

struct Vector2d {
  int x, y;
  bool valid = true;

  Vector2d() { reset(); }
  Vector2d(int x_, int y_) { x = x_; y = y_; valid = true; }
  void reset() { x = 0; y = 0; }
  int operator[](int i) {if (i == 0) { return x; }else{ return y; }}
  Vector2d operator+=(const Vector2d& other) { x += other.x; y += other.y; return *this; }
  Vector2d operator+=(const Vector2f& other) { x += other.x; y += other.y; return *this; }
  Vector2d operator*=(const int& mul) { x = x * mul; y = y * mul; return *this; }
  Vector2d operator/=(const int& div) { x = x / div; y = y / div; return *this; }
  Vector2d operator-(const Vector2d& other) { x -= other.x; y -= other.y; return *this; }
  Vector2d operator+(const Vector2d& other) { return Vector2d(x + other.x, y + other.y); }
};

struct Vector3f {
  float x, y, z;
  Vector3f() { reset(); }
  Vector3f(float x_, float y_, float z_) { x = x_; y = y_; z = z_; }
  void reset() { x = 0; y = 0; z = 0; }
  float operator[](int i) {if (i == 0) { return x; }else if (i == 1) { return y; }else return z;}
  Vector2d operator+=(const Vector2d& other) { x += other.x; y += other.y; return *this; }
  Vector2d operator+=(const Vector2f& other) { x += other.x; y += other.y; return *this; }
  Vector2d operator*=(const int& mul) { x = x * mul; y = y * mul; return *this; }
  Vector2d operator/=(const int& div) { x = x / div; y = y / div; return *this; }
  Vector2d operator-(const Vector2d& other) { x -= other.x; y -= other.y; return *this; }
  Vector2d operator+(const Vector2d& other) { return Vector2d(x + other.x, y + other.y); }
};

struct Vector4d{
  int x, y, z, q;
  Vector4d() { reset(); }
  Vector4d(int x_, int y_, int z_, int q_) { x = x_; y = y_; z = z_; q = q_; }
  void reset() { x = 0; y = 0; z = 0; q = 0;}
  int operator[](int i) {if (i == 0) { return x; }else if (i == 1) { return y; }else if(i == 3) {return z; }else return q;}
  Vector4d operator+=(const Vector4d& other) { x += other.x; y += other.y;  z += other.z; q += other.q; return *this; }
  Vector4d operator+=(const Vector4d& other) { x += other.x; y += other.y;  z += other.z; q += other.q;  *this; }
  Vector4d operator*=(const int& mul) { x = x * mul; y = y * mul; z = z * mul; q = q * mul; return *this; }
  Vector4d operator/=(const int& div) { x = x / div; y = y / div; z = z / div; q = q / div; return *this; }
  Vector4d operator-(const Vector4d& other) { x -= other.x; y -= other.y; z -= other.z; q -= other.q; return *this; }
  Vector4d operator+(const Vector4d& other) { return Vector4d(x + other.x, y + other.y, z + other.z, q + other.q); }
};

struct Vector4f{
  float x, y, z, q;
  Vector4f() { reset(); }
  Vector4f(int x_, int y_, int z_, int q_) { x = x_; y = y_; z = z_; q = q_; }
  void reset() { x = 0.f; y = 0.0f; z = 0.0f; q = 0.0f;}
  float operator[](int i) {if (i == 0) { return x; }else if (i == 1) { return y; }else if(i == 3) {return z; }else return q;}
  Vector4f operator+=(const Vector4f& other) { x += other.x; y += other.y;  z += other.z; q += other.q; return *this; }
  Vector4f operator+=(const Vector4f& other) { x += other.x; y += other.y;  z += other.z; q += other.q;  *this; }
  Vector4f operator*=(const float& mul) { x = x * mul; y = y * mul; z = z * mul; q = q * mul; return *this; }
  Vector4f operator/=(const float& div) { x = x / div; y = y / div; z = z / div; q = q / div; return *this; }
  Vector4f operator-(const Vector4f& other) { x -= other.x; y -= other.y; z -= other.z; q -= other.q; return *this; }
  Vector4f operator+(const Vector4f& other) { return Vector4f(x + other.x, y + other.y, z + other.z, q + other.q); }
};
  
  
### std::vector自作


#include <locale.h>

#include <memory>
#include <memory.h>


#define MY_ASSERT(A) assert(A)

#ifndef MAX
#define MAX(a, b) ((a) > (b) ? (a) : (b))
#endif

#ifndef MIN
#define MIN(a, b) ((a) < (b) ? (a) : (b))
#endif

#define disp(A) (std::cout << #A << " = " << A << "\n")

template<typename T>
struct uiVector {
  int     Size;
  int     Capacity;
  T*      Data;
  int     DeltaCapacityGrow;

  // Provide standard typedefs but we don't use them ourselves.
  typedef T                   value_type;
  typedef value_type*         iterator;
  typedef const value_type*   const_iterator;

  inline uiVector() { Size = Capacity = 0; DeltaCapacityGrow = 100;  Data = NULL; }
  inline uiVector(const uiVector<T>& src) { Size = Capacity = 0; Data = NULL;  DeltaCapacityGrow = 100; operator=(src); }
  inline uiVector<T>& operator=(const uiVector<T>& src) { clear(); resize(src.Size); memcpy(Data, src.Data, (size_t)Size * sizeof(T)); return *this; }
  inline ~uiVector() { if (Data) free(Data); }

  inline void         setDeltaCapacityGrow(int A) { DeltaCapacityGrow = A; }
  inline bool         empty() const { return Size == 0; }
  inline int          size() const { return Size; }
  inline int          size_in_bytes() const { return Size * (int)sizeof(T); }
  inline int          capacity() const { return Capacity; }
  inline T&           operator[](int i) { MY_ASSERT(i < Size); return Data[i]; }
  inline const T&     operator[](int i) const { MY_ASSERT(i < Size); return Data[i]; }

  inline void         clear() { DeltaCapacityGrow = 100; if (Data) { Size = Capacity = 0; free(Data); Data = NULL; } }
  inline T*           begin() { return Data; }
  inline const T*     begin() const { return Data; }
  inline T*           end() { return Data + Size; }
  inline const T*     end() const { return Data + Size; }
  inline T&           front() { MY_ASSERT(Size > 0); return Data[0]; }
  inline const T&     front() const { MY_ASSERT(Size > 0); return Data[0]; }
  inline T&           back() { MY_ASSERT(Size > 0); return Data[Size - 1]; }
  inline const T&     back() const { MY_ASSERT(Size > 0); return Data[Size - 1]; }

  inline bool  _grow_capacity(int num) {
    T* p_new = (T*)realloc(Data, (num + Capacity) * sizeof(T));
    if (p_new == NULL) {
      std::cout << "メモリ確保失敗"; MY_ASSERT(p_new == NULL); return false;
    }
    else { memcpy(p_new, Data, Capacity * sizeof(int));  Data = p_new; Capacity += num; return true; }
  }

  inline void push_back(const T& v) {
    if (Size == Capacity) _grow_capacity(DeltaCapacityGrow);
    memcpy(&Data[Size], &v, sizeof(v)); Size++;
  }


  inline void         pop_back() { MY_ASSERT(Size > 0); Size--; }

  inline void         resize(int new_size) { if (new_size > Capacity) _grow_capacity(new_size - Capacity); Size = new_size; }
  inline void         resize(int new_size, const T& v) { if (new_size > Capacity) _grow_capacity(new_size - Capacity); if (new_size > Size) for (int n = Size; n < new_size; n++) memcpy(&Data[n], &v, sizeof(v)); Size = new_size; }
  inline void         shrink(int new_size) { MY_ASSERT(new_size <= Size); Size = new_size; } // Resize a vector to a smaller size, guaranteed not to cause a reallocation

  inline void         display() { 
    std::cout << "data = ["; for(int i = 0; i < Size; i++) { std::cout << Data[i] << ", "; } std::cout << "\n"; }
};

### WinMainのプログラムでもコンソール出力を可能にする

#include <Windows.h>
# include <stdio.h>
# include <iostream>

int WINAPI WinMain(HINSTANCE hCurInst, HINSTANCE hPrevInst, LPSTR lpsCmdLine, int nCmdShow) {
	AllocConsole();
	freopen("CONOUT$", "w", stdout);
	freopen("CONIN$", "r", stdin);

	std::cout << "Hello World!!";
  printf("HELLO WORLD!\n");

  //色々処理

  FreeConsole();
}
  
  
### 関数マクロ

#ifndef MAX
#define MAX(a, b) ((a) > (b) ? (a) : (b))
#endif

#ifndef MIN
#define MIN(a, b) ((a) < (b) ? (a) : (b))
#endif

#define MAX3(a,b,c)  MAX(MAX((a), (b)), (c))
#define MIN3(a,b,c)  MIN(MIN((a), (b)), (c))

#define VALUE_MAP(x, in_min, in_max, out_min, out_max) ( ((x) - (in_min)) * ((out_max) - (out_min)) / ((in_max) - (in_min)) + (out_min))

### スピーカーの音量変更

#include <Windows.h>
#include <stdio.h>
#include <mmdeviceapi.h>
#include <endpointvolume.h>

//マスターの音量を調整する
// newVolume : スピーカーの音量の設定値。0.0（Mute）～1.0（最大音量）
void setVolume(double newVolume) {
  HRESULT hr;

  CoInitialize(NULL);
  IMMDeviceEnumerator *deviceEnumerator = NULL;
  hr = CoCreateInstance(__uuidof(MMDeviceEnumerator), NULL, CLSCTX_INPROC_SERVER, __uuidof(IMMDeviceEnumerator), (LPVOID *)&deviceEnumerator);
  IMMDevice *defaultDevice = NULL;

  hr = deviceEnumerator->GetDefaultAudioEndpoint(eRender, eConsole, &defaultDevice);
  deviceEnumerator->Release();
  deviceEnumerator = NULL;

  IAudioEndpointVolume *endpointVolume = NULL;
  hr = defaultDevice->Activate(__uuidof(IAudioEndpointVolume), CLSCTX_INPROC_SERVER, NULL, (LPVOID *)&endpointVolume);
  defaultDevice->Release();
  defaultDevice = NULL;


  // float currentVolume = 0;
  // endpointVolume->GetMasterVolumeLevel(&currentVolume);
  // printf("Current volume in dB is: %f\n", currentVolume);
  // hr = endpointVolume->GetMasterVolumeLevelScalar(&currentVolume);
  // printf("Current volume as a scalar is: %f\n", currentVolume);


  // hr = endpointVolume->SetMasterVolumeLevel((float)newVolume, NULL);
  hr = endpointVolume->SetMasterVolumeLevelScalar((float)newVolume, NULL);


  endpointVolume->Release();
  CoUninitialize();
}

### マイクの入力レベル変更

#include <Windows.h>
#include <stdio.h>
#include <mmdeviceapi.h>
#include <endpointvolume.h>

// newVolume : マイクの入力レベルの設定値。0.0（Mute）～1.0（最大音量）
void setMicVolume(double newVolume) {
  HRESULT hr;

  CoInitialize(NULL);
  IMMDeviceEnumerator *deviceEnumerator = NULL;
  hr = CoCreateInstance(__uuidof(MMDeviceEnumerator), NULL, CLSCTX_INPROC_SERVER, __uuidof(IMMDeviceEnumerator), (LPVOID *)&deviceEnumerator);
  IMMDevice *defaultDevice = NULL;

  hr = deviceEnumerator->GetDefaultAudioEndpoint(eCapture, eConsole, &defaultDevice);
  deviceEnumerator->Release();
  deviceEnumerator = NULL;

  IAudioEndpointVolume *endpointVolume = NULL;
  hr = defaultDevice->Activate(__uuidof(IAudioEndpointVolume), CLSCTX_INPROC_SERVER, NULL, (LPVOID *)&endpointVolume);
  defaultDevice->Release();
  defaultDevice = NULL;


  // float currentVolume = 0;
  // endpointVolume->GetMasterVolumeLevel(&currentVolume);
  // printf("Current volume in dB is: %f\n", currentVolume);
  // hr = endpointVolume->GetMasterVolumeLevelScalar(&currentVolume);
  // printf("Current volume as a scalar is: %f\n", currentVolume);


  // hr = endpointVolume->SetMasterVolumeLevel((float)newVolume, NULL);
  hr = endpointVolume->SetMasterVolumeLevelScalar((float)newVolume, NULL);


  endpointVolume->Release();
  CoUninitialize();
}