# 中间层装配（midlayer/）—— 只放协议栈
#
# 本层自包含：上层只 add_subdirectory(midlayer)，内部子目录由本文件负责。
#
# 子目录职责：
#   usb/core/       USB 枚举 / CDC / 描述符 / 端点
#   ethercat/core/  AL 状态机 / 邮箱 / SDO / PDO
#
# 门面（Usb : Stream、EthercatSlave<Rx,Tx>）在 framework/drivers/，不在这里。
# 驱动（Pwm / Output / Input / Can / Spi / UartDma / Rs485）也在 framework/drivers/。

if(CONFIG_DUST_USB_DEVICE_STACK)
    target_sources(app PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/usb/core/usb_dev_port.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/usb/core/usb_cdc_acm.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/usb/core/usb_descriptor.cpp
    )
    target_include_directories(app PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/usb/core
        ${CMAKE_CURRENT_SOURCE_DIR}/../zephyr/contract
    )
endif()

# EtherCAT 协议栈
#
# core/ 按职责分五层：
#   spec/     纯数据与常量，无状态（limits / registers / wire / object / application_objects）
#   model/    对象模型（dictionary）
#   pdo/      过程数据域（process_data）
#   mailbox/  邮箱链路（mailbox / sdo / sdo_info / coe / sii_service）
#   al/       状态机与时基（state_machine / sync / sm）
#   core/     组合根（device）
if(CONFIG_DUST_COM_ETHERCAT)
    target_sources(app PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core/diag/ecat_diagnostics.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core/model/ecat_dictionary.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core/pdo/ecat_process_data.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core/mailbox/ecat_mailbox.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core/mailbox/ecat_sdo.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core/mailbox/ecat_sdo_info.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core/mailbox/ecat_coe.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core/mailbox/ecat_sii_service.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core/al/ecat_state_machine.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core/al/ecat_sync.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core/al/ecat_sm.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core/ecat_device.cpp
    )
    target_include_directories(app PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core
        ${CMAKE_CURRENT_SOURCE_DIR}/../zephyr/contract
    )
endif()
