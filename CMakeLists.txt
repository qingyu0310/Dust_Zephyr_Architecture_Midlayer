# 中间层：只放协议栈（USB 枚举/CDC、EtherCAT AL 状态机/邮箱/SDO/PDO）
#
# 驱动（Pwm / Output / Input / Encoder / Can / Spi / UartDma / Rs485）在 framework/drivers/，
# 不在这里。契约头（usb_hal.hpp / ecat_esc.hpp 等）在 zephyr/contract/。
#
# 装配方式与 framework 各层同构：Kconfig 门禁 → 源文件进 app target。

# USB 协议栈（门面 usb.cpp + core/ 三个源文件）
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

if(CONFIG_DUST_COM_USB)
    target_sources(app PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/usb/usb.cpp)
    target_include_directories(app PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/usb
        ${CMAKE_CURRENT_SOURCE_DIR}/usb/core
        ${CMAKE_CURRENT_SOURCE_DIR}/../zephyr/contract
        ${CMAKE_CURRENT_SOURCE_DIR}/../framework/drivers/communication/stream/uart
        ${CMAKE_CURRENT_SOURCE_DIR}/../framework/drivers/communication/stream
    )
endif()

# EtherCAT 从站（C++ 实现）
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
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat
        ${CMAKE_CURRENT_SOURCE_DIR}/ethercat/core
        ${CMAKE_CURRENT_SOURCE_DIR}/../zephyr/contract
    )
endif()
