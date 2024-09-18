Excel chart의 지정된 시각에 triggered CT 수행해 자동 전송
==================================================

.. attention::

    시:분 까지만 확인한다. 현재시각에서 지정된 시각과 분이 동일하면 CT를 수행한다. 만약, 현재시각이 지정된 시각을 지났으면 다음 지정된 시각이 되어야 CT를 수행한다


.. attention::

    자정을 지났을 때 어떻게 동작하는지는 확인이 필요함


#. .py script를 작성 또는 편집한다. script가 모여 있는 folder는

    ::

        C:\codes\run-scripts\

#. 수정 후 저장한다
#. CT 수행 시각을 아래 excel file에 기록한다

    ::

        C:\codes\bl6c_daq\sample.xlsx

#. script를 실행한다

    .. code-block:: Python

        %load script파일명

    .. tip::

        intellisense가 되는데, ``End`` key를 누른다

    script code를 review하도록 다시 한 번 보여준다. \
    ``Enter`` key를 한 번 더 눌러 줘야 실행이 된다

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
        :emphasize-lines: 26, 122-123
        
        cond = {
            'exptime': 100,          # Expose time in milliseconds
            'numprj': 1181,         # Number of frames
            'pathname': 'wind',     # Pathname

            'numiter': 100,          # Number of scan iteration
            
            'x2origin': -2852.00,   # Sample stage origin
            'pullback': 5500,     # X2_Pullback distance(um)
            'numff': 10,             # Number of flat fields
            'numdf': 10,             # Number of dark fields
            'default_speed': 4,     # Sample stage speed
            'uuid': str(uuid.uuid4()), # Scan uuid
            'stop_angle': 200,      # Target angle
        }
        import datetime
        import pandas as pd

        cond['scan_speed'] = 0.5 * 60000 / (cond['numprj']) / (cond['exptime']+60) # Rot./min.
        # cond['start_angle'] =  (-1)*180/(cond['numprj']-1)*5 + 0 # Start angle
        cond['start_angle'] =  -5 # Start anglecond['start_angle'] =  (-1)*180/(cond['numprj']-1)*5 + 0 # Start angle

        logger.info(f"Scan parameters : {cond}")

        # Load time list
        data = pd.read_excel('C:\\codes\\bl6c_daq\\sample.xlsx')
        times = data['time']

        time_list = []
        for it in times:
            time_list.append(it.strftime("%H:%M"))
            
        print(f"time_list : {time_list}")

        def finalize_pco(): #script stop code
            # Close shutter
            yield from bps.mv(shutter, 0)
            # Set exposure time in seconds
            yield from bps.mv(pco.cam.acquire_time, cond['exptime']/1000,
                            pco.cam.trigger_mode, 0) # Internal trigger
            yield from bps.abs_set(sample.rot_stop, 1)
            yield from bps.mv(sample.rot.velocity, cond['default_speed'])
            yield from bps.mv(sample.rot, 0)
            yield from bps.mv(sample.x2, cond['x2origin'])
            yield from bps.sleep(1)
            
        
        @finalize_decorator(finalize_pco)
        def time_trigger():
            """
            """

            # Enable saving
            pco.save_image(True)
            # Stats calculation is not needed
            pco.enable_stats(False)
            
            ct = 1
            # CT scan
            # for _ in range(cond['numiter']):
            for _ in range(len(time_list)):
                
                ## Wait for specfied time 
                while True:
                    current_time = datetime.datetime.now().strftime("%H:%M")
                    print(f"current_time : {current_time}")
                    if current_time in time_list:
                        time_list.remove(current_time)
                        logger.info(f"Scan triggered : {current_time}")
                        break
                    else:
                        yield from bps.sleep(1)
                
                logger.info(f"Moving sample.rot to {cond['start_angle']}, x2 to {cond['x2origin']}")
                yield from bps.mv(sample.rot.velocity, cond['default_speed'])
                yield from bps.mv(sample.rot, cond['start_angle'],
                                sample.x2, cond['x2origin'])

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

                # Flat fields
                logger.info(f"Measure Flat field : {cond['numff']} frames")
                
                yield from bps.mvr(sample.x2, cond['pullback'])
                #yield from bps.mvr(sample.wireless_x, cond['pullback'])
                #yield from bps.mvr(sample.z, cond['pullback'])
                
                yield from bps.mv(shutter, 1)    
                yield from bp.count([pco],
                                    num=cond['numff'],
                                    md={'reason': 'flat-field',
                                        'uuid': cond['uuid'],
                                        'settings': cond})
                yield from bps.mv(shutter, 0) 

                yield from bps.mv(sample.rot, cond['start_angle'])
                yield from bps.sleep(1) # 1 second    
                
                yield from bps.mvr(sample.x2, -1*cond['pullback'])
                #yield from bps.mvr(sample.wireless_x, -1*cond['pullback'])
                #yield from bps.mvr(sample.z, -1*cond['pullback'])

                start_position = yield from bps.rd(sample.rot)
                logger.info(f"Start CT scan at {datetime.datetime.now()}, motor start position : {start_position}")
                
                yield from bps.mv(sample.rot, cond['start_angle'])      
                yield from bps.abs_set(sample.rot_stop, 1)
                yield from bps.mv(shutter, 1)                      
                yield from bps.mv(sample.rot.velocity, cond['scan_speed'],
                                pco.cam.trigger_mode, 4,
                                pco.cam.num_images, cond['numprj']) # External trigger mode
                yield from bps.abs_set(sample.rot, 200)
                yield from bps.sleep(cond['exptime']/1000*5)
                yield from bp.count([pco], md={'reason' : 'CT scan',
                                                'uuid' : cond['uuid'],
                                                'settings': cond})
                stop_position = yield from bps.rd(sample.rot)
                yield from bps.mv(shutter, 0)

                logger.info(f"Finished CT scan at {datetime.datetime.now()}, stop position : {stop_position}")
                logger.info(f"{ct} Finished!")
                ct = ct+1
                
                yield from bps.abs_set(sample.rot_stop, 1)
                yield from bps.sleep(1)
                yield from bps.mv(sample.rot.velocity, cond['default_speed'])
                yield from bps.sleep(1)
                
                yield from bps.mv(sample.rot, cond['start_angle'])         
                
                cond['uuid'] = str(uuid.uuid4()),


            pco.save_image(False)
            

        # Run the plan
        #logger.info(f"Start CT scan at {datetime.datetime.now()}")
        RE(time_trigger())


