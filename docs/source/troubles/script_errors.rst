Script error 시 대처
==========================

The plugin hdf1 on the area detector with name hama has not been primed.
-------------------------------------------------

+ ``카메라서버Hama`` 를 reboot했을 경우 발생함
#. ``IPython`` terminal에서 아래를 실행한다

    .. code-block:: Python

        hama.hdf1.warmup()

