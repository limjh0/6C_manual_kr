단순히 FF와 DF 촬영해 전송하기
===============================

#. .py script를 작성 또는 편집한다. script가 모여 있는 folder는

    ::

        C:\codes\run-scripts\

#. 수정 후 저장한다
#. script를 실행한다

    .. code-block:: Python

        %load script파일명

    .. tip::

        intellisense가 되는데, ``End`` key를 누른다

    .. tip::

        강제로 멈추고자 하면 ``ctrl+C``

#. Scan 완료 후 data가 자동으로 **storage server** 로 넘어간다
    + Local (즉, **수집PC** ) 내 데이터 저장 위치

        ::

            Y:/Data/db/년도/월/일
            Y:/Data/db/2024/09/09

    + 데이터를 **storage server** 로 전송 완료 후에 local의 데이터를 지운다 (저장 위치 folder는 남겨 둠)

    .. warning::

        Scan 완료가 아니라 cancel된 경우는 (가령 ``ctrl+C`` ) 데이터가 local에 남아 있음 -> 수동으로 지워야 함


Script 예:

    .. code-block:: Python
        
        cond = {
        'exptime': 50,          # Expose time in milliseconds
        
        'numprj': 1,         # Number of frames   
        'pathname': 'FF_DF',     # Path name
        'numff': 5,             # Number of flat fields
        'numdf': 5,             # Number of dark fields
        'uuid': str(uuid.uuid4()), # Scan uuid
        'pullback': 0,     # Pullback distance
        'x2origin': -2910.00,   # Sample stage origin
        #'default_speed': 4,     # Sample stage speed
        #'stop_angle': 200,      # Target angle
        }
        #cond['scan_speed'] = 4 # Rot./min.
        #cond['start_angle'] = 0 # Start angle

        logger.info(f"Scan parameters : {cond}")

        #pco.save_image(True)

        @finalize_decorator(finalize)
        def time_trigger():
            """
            """
            # Enable saving
            pco.save_image(True)
            # Stats calculation is not needed
            pco.enable_stats(False)


            #logger.info(f"Moving sample.rot to {cond['start_angle']}, x2 to {cond['x2origin']}")
            #yield from bps.mv(sample.rot.velocity, cond['default_speed'])
            #yield from bps.mv(sample.rot, cond['start_angle'],
            #                 sample.x2, cond['x2origin'])

            # Set exposure time in seconds
            yield from bps.mv(pco.cam.acquire_time, cond['exptime']/1000,
                            pco.cam.num_images, 1,
                            pco.cam.trigger_mode, 0) # Internal trigger
            
            
            # Dark fields
            logger.info(f"Measure Dark field : {cond['numdf']} frames")
            yield from bps.mv(shutter, 0) # Close shutter
            yield from bp.count([pco],
                                num=cond['numdf'],
                                md={'reason': 'dark-field',
                                    'uuid': cond['uuid'],
                                    'settings': cond})
            
            #yield from bps.abs_set(sample.rot, 90)

            # Flat fields
            logger.info(f"Measure Flat field : {cond['numff']} frames")
            #yield from bps.mvr(sample.x2, cond['pullback'])
            #yield from bps.mvr(sample.wireless_x, cond['pullback'])
            yield from bps.mv(shutter, 1)    
            yield from bp.count([pco],
                                num=cond['numff'],
                                md={'reason': 'flat-field',
                                    'uuid': cond['uuid'],
                                    'settings': cond})
            yield from bps.mv(shutter, 0)                                
            #yield from bps.mvr(sample.x2, -1*cond['pullback'])
            #yield from bps.mvr(sample.wireless_x, -1*cond['pullback'])
            #yield from bps.abs_set(sample.rot, 0)
            
            pco.save_image(False)

        RE(time_trigger())

