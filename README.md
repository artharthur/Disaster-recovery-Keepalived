# Disaster-recovery-Keepalived

В этом репозитории хранится решение домашнего задания по настройке HSRP-трекинга в Cisco Packet Tracer.

## Содержимое

- `task1.pkt` — проект Cisco Packet Tracer с топологией и настройками:
  - HSRP группа 0 отслеживает Gi0/1
  - HSRP группа 1 отслеживает Gi0/0
- `task1.png` — скриншот процесса тестирования: отключение Gi0/0 и успешный `ping` VIP 192.168.1.1

## Как проверить

1. Откройте `task1.pkt` в Cisco Packet Tracer.  
2. Убедитесь, что в конфигурации:
   - На Router1 и Router2 в интерфейсе Gi0/0 настроена команда:
     ```
     standby 1 track GigabitEthernet0/0
     ```
3. В режиме **Simulation**:
   - Удалите кабель между Router1 Gi0/0 и Switch0.  
   - На PC0 выполните `ping 192.168.1.1`.  
   - Убедитесь, что после короткой паузы (Failover) ответы идут от Router2.

## Дополнительно

- Для полного восстановления следует настроить обратный трекинг и автоматическое восстановление при поднятии интерфейса.  
- См. лекционный материал по HSRP и VRRP в соответствии с методическими указаниями.

---

> Репозиторий создан автоматически после выполнения задания 1.

## Задание 2: Keepalived + health-check

На двух нодах (Ubuntu VM и Debian) настроен Keepalived с запуском скрипта `check_web.sh` каждые 3 с:

- **global_defs**:  
  ```conf
  global_defs {
    script_user root
  }

•	vrrp_script:
vrrp_script chk_web {
  script "/usr/local/bin/check_web.sh"
  interval 3
  weight -2
}

•	vrrp_instance:
vrrp_instance VI_1 {
  state MASTER|BACKUP
  interface <имя-интерфейса>
  virtual_router_id 51
  priority <150|100>
  advert_int 1

  track_script {
    chk_web
  }

  virtual_ipaddress {
    10.211.55.19/24
  }
}
