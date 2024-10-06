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


#. **OP현미경** 을 빔 위치로 이동시킨다. 이를 위해 ``Optical table`` 을 이동한다 (축 이름이 무엇??)
    #. ``Image Viewer`` form에 ``Sync`` 를 check 한다: 카메라와 shutter를 동기화함

        .. image:: images/0053_Image_viewer.png
            :align: center

    #. **OP현미경** 코에 형광판을 달아서 빔 위치를 확인한다

        .. image:: images/0056_fluorescent_screen.png
            :align: center

    #. ``Hamamatsu camera`` 로 빔을 확인한다
    #. **Slit 2** 를 조정한다
    #. **Long movable mask** 를 조정한다
#. **Sample stage** 를 옮겨서 설치한다: ``Optical table`` 위의 표시된 위치로 옮기고, 고정할 두 개의 볼트가 screw홀에 잘 들어가면 **sample stage** 를 ``Optical table`` 길이 방향으로 밀어서 (유격이 있음) 이전 위치와 같게 한다
    
    .. image:: images/0057_sample_stage.png
        :align: center

#. **OP현미경** 의 대물렌즈를 확인하고, 필요시 교체한다

    .. _focusing:

#. **OP현미경** 의 초점을 맞춘다:
    #. 초점을 맞추기 위한 target object를 **sample stage** 에 거치한다

        .. image:: images/0060_plywood.png
            :align: center

    #. ROI 지정하기 위해 ``Box`` button을 누른다

        .. image:: images/0070_box_button.png
            :align: center

    #. 빔이 상하로 움직이는 것이 영향을 주므로, 빔 밝기가 변하지 않는 지점에 ROI를 만든다
    #. auto-focus를 실행한다: 가령, step size는 7 um, 횟수 21, 노출시간 0.1초

        .. code-block:: Python

            RE(screen_align_cen(hama, sample.camera_focus, 대략적인 초점값, 7, 21, time=0.1)
            RE(screen_align_cen(hama, sample.camera_focus, 대략적인 초점값, step size, 횟수 (홀수여야 함), time=0.1)

    #. Graph가 그려진다. 이 때, ``Total`` 은 불필요하므로 uncheck할 것

        .. image:: images/0080_scintillator_align_graph.png 
            :align: center

    #. Graph가 그려지면 ``scintillation screen`` 의 tilt 제어를 자동으로 할 것인지 물어 봄. \ ``y`` 는 자동; ``n`` 은 수동

        .. note::

            **OP현미경** 은 tilt 제어가 수동으로만 가능함

        .. attention::

            ``n`` 을 누를 것. 

        .. image:: images/0090_tilt_adjust.png
            :align: center

    #. ``OP현미경`` 의 수동 tilt 조정:
        + 수평조정 계산값이
            + 양수: ``C`` 를 시계 방향으로 돌린다; 또는 ``B`` 를 반시계 방향으로 돌린다
            + 음수: ``C`` 를 반시계 방향으로 돌린다; 또는 ``B`` 를 시계 방향으로 돌린다  
        + 수직조정 계산겂이
            + 양수: ``A`` 를 시계 방향으로 돌린다; 또는 ``B`` 와 ``C`` 를 모두 반시계 방향으로 돌린다
            + 음수: ``A`` 를 반시계 방향으로 돌린다; 또는 ``B`` 와 ``C`` 를 모두 시계 방향으로 돌린다 
    
            .. image:: images/0100_OP_scintillator_align.png
                :align: center

    #. Scintillator tilt를 조절했으면 auto-focus를 재실행해서 제대로 보정되었는지 확인한다
    #. **OP현미경** ``focus`` 가 찾아진 값으로 되어 있는지도 확인한다
    #. 최종적으로, ``Enable`` button을 눌러서 ``Stats`` LED가 켜지게 한다

        .. image:: images/0110_enable_button.png
            :align: center

#. :doc:`CT 정렬 <CT정렬>` 한다
#. :doc:`Script <../scripting/home>` 를 작성한다

