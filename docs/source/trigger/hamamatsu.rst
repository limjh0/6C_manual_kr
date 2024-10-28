Trigger 설정 ( ``Rotataion stage`` 와 ``Hamamatsu카메라`` 동기화)
=================================================================
``pco.Edge카메라`` 를 사용하다가 ``Hamamatsu카메라`` 로 변경하는 것으로 가정함.

#. *원격데스크탑* 으로 **수집PC** 에 접속한다 (10.10.62.115)
#. ``Elmo Application Studio II`` 를 찾는다
#. ``Motion - Single Axis`` 에서 ``-5도`` 로 보낸 후 ``Disable`` 을 누른다

    .. image:: images/0140_elmo_start_angle.png
        :align: center

#. ``Application Tools`` 에서 ``Stop`` 을 누른다. ``Digital Output Selection`` 을 ``Output 1`` 로 한다
    .. image:: images/0150_elmo_output_selection.png
        :align: center
    
    .. note::

        ``Output 2`` 는 ``pco.Edge카메라`` 이다

#. ``Start`` 버튼을 누른다
    .. image:: images/0160_elmo_start_button.png
        :align: center

#. ``Motion - Single Axis`` 에서 ``Enable`` 버튼을 누른다
    .. image:: images/0170_elmo_enable_button.png
        :align: center

.. warning::

    ``Enable`` button 왼쪽에 있는 ``0`` button은 누르면 안 됨!!! 현재 각도를 0으로 설정함. 
    :ref:`혹시, ``0`` button을 눌렀을 경우 <0_button>` 

#. 제대로 trigger 되는지 확인하기:

    + ``Rotation stage`` 를 ``-5도`` 로 보낸 후, ``0도`` 지나게 회전시키면서,
    ``Generated Pulses OC[12]`` 에 값이 증가하는지 확인하기
        .. image:: images/0200_elmo_generated_pulses.png
            :align: center

    + ``Hamamatsu카메라`` 에서 영상이 올라오는지 확인하기
        #. :doc:`trigger mode를 ``external`` 로 변경 <camera별setting_command>` 
        #. ``Rotation stage`` 를 ``-5도`` 로 보낸 후, ``0도`` 지나게 회전시키면서, 카메라 화면이 올라오는지 확인한다
        #. 이후 작업을 편리하게 하기 위해 trigger mode를 ``internal`` 로 변경한다

.. _0_button:

혹시, ``Elmo Application Studio II`` 에서 ``0`` button을 누르면 현재 각도를 0으로 설정함:

    .. image:: images/0180_elmo_zero_button.png
        :align: center

이 경우, 
    #. ``Rotation stage`` 를 10도 이상 (권장 90도) 정도로 가져다 놓고
    #. **EPICS CSS** 의 해당 control을 찾아 ``HOM`` button을 누른다

        .. image:: images/0190_css_HOM_button.png
            :align: center
