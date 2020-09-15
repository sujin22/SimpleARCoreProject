# SimpleARCoreProject
# [스터디02] ARCore - Sceneform

Created: Sep 14, 2020
Created by: 수진 양
Tag: Study, android

# Sceneform

OpenGL을 배우지 않고도 AR 및 비 AR앱에서 사실적인 3D 장면을 쉽게 렌더링 할 수 있도록 하는 SDK

include: 

- 높은 수준의 그래프 api
- 사실적인 물리베이스 렌더러(filament에서 제공하는)
- 3D 에셋을 import, view, build하기 위한 Android Studio Plugin

---

## sample project

github에서 clone해옴

```bash
git clone https://github.com/google-ar/sceneform-android-sdk.git
```

구글링했을 때 나오는 대부분의 예제: helloSceneform

>> repository가 achieved? 되었다고 뜨면서 해당 코드 찾을 수 없음

대신 gltf 라는 프로젝트 실행해보았더니 다른 예제는 찾을 수 있었음

실행 결과

![%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled.png](%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled.png)

## 외부 에셋으로 모델 바꾸기

### 플러그인 설치

Setting > Plugins : Google Sceneform Tools (Beta) 설치

![%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled%201.png](%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled%201.png)

### 에셋 가져오기

[poly.google.com](http://poly.google.com) 에서 원하는 에셋의 obj파일 다운로드

[Wolf](https://poly.google.com/view/46bXrRt8pFF)

ex) Wolves

 

android에서

app 폴더 내부에 **sampledata 폴더**를 만든 후

Wolves.mtl, Wolves.obj 파일을 복사해 붙여넣는다.

![%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled%202.png](%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled%202.png)

res폴더 내부에 raw폴더 만들어 줌

(res> new > folder > raw resource ~~)

wolves.obj파일에서 오른쪽 클릭 > Import Sceneform Asset

(파일 이름에 대문자가 허용되지 않으므로 소문자로 바꿔줘야함)

![%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled%203.png](%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled%203.png)

sfa output 경로는 그대로 두고

sfb 경로만 만들어둔 raw폴더로 변경해준다.

![%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled%204.png](%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled%204.png)

그러면 obj로부터 해당 경로에 sfa, sfb파일이 만들어진다.

이 과정에서 오류 많이 겪어서 구글링을 통해 여러 가지 시도를 해봄

gradle에 다음 코드 추가

```java
//gradle (app)
sceneform.asset('sampledata/wolves.obj', 'default','sampledata/wolves.sfa','src/main/res/raw/wolves')
```

---

broken 누른 후 sync 맞추기

(생각나는 것만 적음)

이러다 그냥 run해봤더니 어느 순간 생성됨.. 여러 개 중 뭐가 해결 법이었는지는 모르겠음

### 가져온 에셋으로 코드 작성하기

activity_main.xml

```java
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <fragment
        android:id="@+id/fragment"
        android:name="com.google.ar.sceneform.ux.ArFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</RelativeLayout>
```

MainActivity.java

```java
package com.example.arcoreproject;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.view.MotionEvent;
import android.widget.Toast;

import com.google.ar.core.Anchor;
import com.google.ar.core.HitResult;
import com.google.ar.core.Plane;
import com.google.ar.sceneform.AnchorNode;
import com.google.ar.sceneform.rendering.ModelRenderable;
import com.google.ar.sceneform.ux.ArFragment;
import com.google.ar.sceneform.ux.BaseArFragment;
import com.google.ar.sceneform.ux.TransformableNode;

public class MainActivity extends AppCompatActivity {
    private ArFragment arFragment;
    private ModelRenderable modelRenderable;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        arFragment = (ArFragment)getSupportFragmentManager().findFragmentById(R.id.fragment);
        setUpModel();
        setUpPlane();
    }

    private void setUpModel() {
        ModelRenderable.builder()
                .setSource(this,R.raw.wolves)
                .build()
                .thenAccept(renderable ->modelRenderable = renderable)
                .exceptionally(throwable -> {
                    Toast.makeText(MainActivity.this, "Model can't be Loaded", Toast.LENGTH_SHORT).show();
                    return null;
                });
    }

    private void setUpPlane() {
        arFragment.setOnTapArPlaneListener(new BaseArFragment.OnTapArPlaneListener(){
            @Override
            public void onTapPlane(HitResult hitResult, Plane plane, MotionEvent motionEvent) {
                Anchor anchor = hitResult.createAnchor();
                AnchorNode anchorNode = new AnchorNode(anchor);
                anchorNode.setParent(arFragment.getArSceneView().getScene());
                createModel(anchorNode);
            }
        });
    }

    private void createModel(AnchorNode anchorNode) {
        TransformableNode node = new TransformableNode(arFragment.getTransformationSystem());
        node.setParent(anchorNode);
        node.setRenderable(modelRenderable);
        node.select();
    }

}
```

### **ArFragment**

ARCore의 기본 구성을 사용하는 fragment (public class)

[ArFragment | Sceneform (1.15.0) | Google Developers](https://developers.google.com/sceneform/reference/com/google/ar/sceneform/ux/ArFragment)

**내부 메소드**

- **setOnTapArPlaneListener:**

    ARCore 평면을 탭할 때 호출 할 콜백을 등록함

    - **BaseArFragment.OnTapArPlaneListener** - public static interface

        >> **onTapPlane**: 

        이 인터페이스 내부의 public method

        ARCore 평면이 tap되었을 때 호출됨

        매개 변수?

        ![%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled%205.png](%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled%205.png)

- **getArSceneview**:

    이 fragment의 ArSceneView를 return한다.

    >> scene을 render하는 SurfaceView

- **getTransformationSystem**:

    TransformNode에서 동작을 감지하고, 선택된 노드를 조정하는 데에 사용되는 transformation system을 가져온다.

### ModelRenderable

3D모델을 렌더링한다.

[ModelRenderable | Sceneform (1.15.0) | Google Developers](https://developers.google.com/sceneform/reference/com/google/ar/sceneform/rendering/ModelRenderable#nested-classes)

**내부 메소드**

- **builder:**

    ModelRenderable을 construct함

- **setSource:**

    ![%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled%206.png](%5B%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B502%5D%20ARCore%20-%20Sceneform%207ee77f03958547f681beefc030505f73/Untitled%206.png)

    Renderable의 context(this) 에 resource(R.raw.wolves) 값을 준다.

- **build:**

    builder의 parameter들로 Renderable을 construct함

    **CompletableFuture**를 return함

    >> 비동기 계산의 결과를 제공

- **thenAccept:**

    return된 CompletableFuture 작업의 콜백메서드로 전달된 consumer를 실행함

- **exceptionally:**

    에러 처리

    CompletableFuture 작업을 진행하던 중 에러가 발생했을 때 처리

### Anchor

실제 세계에서 고정된 위치, 방향

### AnchorNode

ARCore Anchor를 기반으로 공간에 자동으로 포지셔닝되는 노드

**내부 메소드**

- **setParent:**

    해당 노드의 부모 노드를 바꿈

### TransformableNode

동작에 의해 select, translate, rotate, scale될 수 있는 Node

내부 메소드

- **setRenderable**:

    이 노드를 display할 Renderable을 설정한다. 

- **select**:

    현재 선택된 노드가 없거나, 현재 선택된 노드가 활발하게 변환되고 있지 않으면

    이 노드를 TransformationSystem에서 선택된 노드로 설정함

---

참고

[com.google.ar.sceneform | Sceneform (1.15.0) | Google Developers](https://developers.google.com/sceneform/reference/com/google/ar/sceneform/package-summary)

[https://www.youtube.com/watch?v=GiLra7jntsk](https://www.youtube.com/watch?v=GiLra7jntsk)
