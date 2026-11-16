# Operations and troubleshooting

## Health checks

Useful commands on the Debian host:

~~~bash
# Container state
docker ps

# Resource usage
btop
free -h
df -h

# Host status helper
iceman-status

# Recent Immich server logs
docker logs --tail 100 immich_server

# Follow logs live
docker logs -f immich_server
~~~

Container names may vary if the Compose project was customized. Use `docker ps` to confirm the actual names.

## Immich job queues

The Immich Administration → Jobs page shows queued and active background work. A queue with zero waiting and zero active jobs indicates that queue has no pending work at that moment. Check all relevant queues after a large import.

## Upload troubleshooting

For Google Photos Takeout migration, the Windows client used immich-go. The terminal running immich-go shows client-side progress and errors; Docker logs show server-side activity.

A WebSocket disconnect in a browser does not by itself prove that an upload has stopped. Check the client process, Immich job queues, server health and final asset counts before drawing conclusions.

## Power and hardware

The Lenovo B570 battery is non-functional. If the charger is unplugged, the machine loses power immediately. Keep the power connection secure and consider a suitable UPS/power arrangement. A clean shutdown uses:

~~~bash
sudo poweroff
~~~

## Resource constraints

The 4 GB RAM system can become heavily loaded during simultaneous uploads, video transcoding, thumbnail generation and machine-learning tasks. Stagger large first-time family uploads if the machine becomes unresponsive or thermal load is excessive.

## Family accounts

Each family member should have their own Immich account and mobile-app login. Separate accounts keep libraries and permissions distinct. Confirm each user's backup settings and upload status independently.
