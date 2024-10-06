``pco.Edge카메라`` 에서 ``Hamamatsu카메라`` 로 변경
===============================================
**PCO1현미경** 또는 **PCO2현미경** 을 사용하다가 **OptiquePeter현미경** (이하, **OP현미경** )으로 변경한다고 가정하겠음

#. **제어PC** 에서 세 개의 terminal들 중, ``tiffWriter`` 를 제외한 ``PyDM`` terminal과 ``IPython`` terminal을 닫는다
#. *원격데스크탑* 으로 **수집PC** 에 접속한다 (10.10.62.115) -> ``pco IOC`` terminal을 닫는다
#. 실험허치에 들어가 **PCO현미경** 의 ``pco.Edge카메라`` 의 전원을 끈다. **OP현미경** 의 ``Hamamatsu카메라`` 의 전원을 켠다

    .. image:: images/0010_hamamatsu_camera.png
        :align: center

#. ``Rotation stage`` 와 동기화를 하려면 :doc:`Trigger <trigger>` 를 설정한다
#. **수집PC** 에서 ``바탕화면`` folder 내 ``hamamatsu_IOC.bat`` 를 실행한다

    .. image:: images/0020_hamamatsu_IOC.png
        :align: center

#. 아래와 같이 비밀번호를 물어옴. 비밀번호: bl6c

    .. image:: images/0030_password.png
        :align: center

#. ``s`` 를 눌러서 실행시킴

    .. image:: images/0040_IOC_start.png
        :align: center

#. **제어PC** 로 전환 후 terminal들을 켠다
    #. ``RunWriter`` 가 실행되어 있는지 확인함
        + ``tiffWriter`` terminal이 켜져 있어야 함: 획득한 영상을 자동으로 **storage server** 로 transfer함
        + 만약, **storage server** ``disk 번호`` 를 변경하려면 다음과 같이 한다
            #. 현재의 세 terminal을 모두 닫는다: ``tiffWriter``, ``PyDM``, ``IPython``
            #. 평소하듯이 **storage server** ``disk 번호`` 를 변경하는데, 드라이브를 ``X:`` 로 지정해야 한다
            #. ``X: 드라이브`` 의 이름이 틀리게 나오면--즉, **storage server** ``disk 번호`` 가 제대로 표기되지 않으면-- 컴퓨터를 reboot해야 함 (괜찮을 수도 있는데, test해 보지 않았음)
    #. ``Param``
        + .txt file임
        + pixel 크기를 입력함 (이후 auto-focus 작업 때 필요함)
    #. ``RunViewer`` 를 실행함
        #. GUI form이 생김

            .. image:: images/0050_GUI_form.png
                :align: center

        #. ``ImageViewer [HAMA]`` button을 누름
        #. auto-focusing을 위해 ``Data Viewer`` button을 누름
        #. motor control을 위해 ``Stages [Manager]`` button을 누름 
            + 암호: qlafkdls 6c
    #. ``RunDAQ`` 를 실행함: script를 실행할 때 사용함
        #. Python terminal임
        #. 카메라 인식을 위해 

            .. code-block:: Python

                setup_hama()

        #. script folder로 이동하기 위해
            .. code-block:: Python

                cd ../run_scripts/

    .. note::

        ``Hamamatsu camera`` 는 time-stamp 기능이 없다. 각 file이름에 시간이 붙는다

#. **OP현미경** 을 정렬한다. 이를 위해 ``Optical table``을 이동한다 (축 이름이 무엇??)
#. 

