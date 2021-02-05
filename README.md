# interface=eth0
dumpdir=/root/dumps
 
while /bin/true; do
  pkt_old=`grep $interface: /proc/net/dev | cut -d :  -f2 | awk '{ print $2 }'`
  sleep 1
  pkt_new=`grep $interface: /proc/net/dev | cut -d :  -f2 | awk '{ print $2 }'`
 
  pkt=$(( $pkt_new - $pkt_old ))
  echo -ne "\r$pkt packets/s\033[0K"
 
  if [ $pkt -gt 10000 ]; then
    echo -e "\n`date` Under Attack. Capturing..."
    pktname="dump_`date +%d-%m-%y_%H:%M:%S`.pcap"
    tcpdump -i $interface -t -w $dumpdir/dump_`date +%d-%m-%y_%H:%M:%S`.pcap -c 10000
    echo "`date` Packets Captured. Sleeping..."
    ./discord.sh \
--webhook-url="https://discord.com/api/webhooks/790958005800665158/kjvHJjToHjNMuxQwofIMszlcQ3e1wMhgL6Z5Txu-Yu2BGm6gGtMPfsZsYepI2JPnZdIZ" \
--username "ParadoxVPN Server Monitor" \
--title "Attack Monitor" \
--description "A DDoS attack on server **OVH MAIN CA** has managed to bypass ovh's vac and the attack has been logged and will be investigated soon.\n\n**Capture Name:** $pktname\n**Capture Location:** $dumpdir\n**Attack PPS:** $pkt\n\n**Server IP:** ||:/||" \
--color "0x700D06" \
--url "https://status.paradoxvpn.com" \
--thumbnail "https://i.imgur.com/ifR053m.png" \
--author "Server Monitor" \
--author-url "https://status.paradoxvpn.com" \
--author-icon "https://i.imgur.com/uxQ36sv.png" \
--image "https://imgur.com/RPb7G2N.png" \
--footer "Server Attack Monitor" \
--footer-icon "https://img.pngio.com/warning-icon-png-321332-free-icons-library-warning-icon-png-2400_2400.jpg" \
--text "Look below for the attack details." \
--timestamp
 
sleep 300
fi
done
