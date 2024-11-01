ImageViewer 동작이상 시 진단 및 조치
==========================

Camera 영상은 들어오는데, statistics 값 (mean, std)이 변하지 않을 때
-------------------------------------------------------------------

+ statistics 값이 변하지 않음
+ 아마도, live stop 기능도 비정상적일 것임 (가령, camera가 계속 켜져 있음)
+ ``ImageViewer`` 를 끄면 다시 켜지지도 않을 것임
+ 위와 같은 증상이 있으면, ``PyDM`` terminal과 ``IPython`` terminal을 껐다가 다시 켜고, ``ImageViewer`` 를 열어 주면 제대로 동작할 것임
+ 또는, ``ImageViewer`` 를 끄고, ``IPython`` terminal에서 아래와 같이 reset 후 ``ImageViewer`` 를 켠다
    .. code-block:: Python

        setup_pco()

