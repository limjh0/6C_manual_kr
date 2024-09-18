CT 정렬
=====================
:ref:`초점정렬 <auto-focusing>` 완료 상태에서 CT 정렬을 수행함.
CT 정렬을 위한 ``target`` 을 mount 하고 beam on 

#. ``Image Viewer`` form에서 ``histogram`` button을 누른다
    .. image:: images/0060_histogram_button.png
        :align: center

#. Centering을 쉽게 하기 위해 ``vertical profile`` button을 누른다
    .. image:: images/0070_vertical_button.png
        :align: center

#. ``target`` 이 아래와 같이 위치하도록 **sample stage** 의 ``Wireless Stage`` 를 조작한다
    + 0도에서 화면 왼쪽
    + 90도에서 화면 중간
    + 180도에서 화면 우측
#. **sample stage** 의 ``X2`` 를 옮겨 대충 centering을 한다 (Excel에서 offset을 계산해 offset 양의 반대만큼 ``X2`` 를 이동시킴)
#. ``vertical profile`` button 우측의 역삼각형을 눌러 펼친 후 ``horizontal profile`` button을 누른다
#. 0도와 180도의 target 높이가 다를 때: **PCO1현미경** 과 **PCO2현미경** 은 ``micrometer 쌍`` 을 수동으로 돌려서 조정한다
    .. image::images/0080_micrometer_pair.png  
        :allign: center
#. 90도의 target 높이가 다를 때: **sample stage**의 ``Ry`` 를 조정한다

