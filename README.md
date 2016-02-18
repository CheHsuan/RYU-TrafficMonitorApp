# RYU-TrafficMonitorApp
This is a RYU application which can monitor hosts traffic and punish hosts if their network traffic are over the certain value. 

Synopsis
==========

In this application, we will use a traffic monitor to listen every port in switch.Each port has 3 queues to output the packets. If the network traffic from a port is higher than the setpoint, then the flow will be leaded to different queue. The first queue enables the bandwith form 800Kbits/sec to 1000Kbits/sec. The second queue enables the bandwidth up to 500 500Kbits/sec and the bandwidth of last queue will be set to 0Kbits/sec.

Step
==========

	1.Mininet

		sudo mn –mac –switch ovsk,protocols=OpenFlow13 –controller remote,ip=127.0.0.1,port=6633 
	
	2.OpenFlow setting
	
		set Bridge s1 protocols=OpenFlow13
	
		set-manager ptcp:6632
	
	3.Run controller
	
		ryu-manager ryu.app.rest_qos ryu.app.traffic_qos ryu.app.rest_conf_switch
	
	4.Set ovsdb_addr
	
		curl -X PUT -d '"tcp:127.0.0.1:6632"' http://localhost:8080/v1.0/conf/switches/0000000000000001/ovsdb_addr
	
	5.Define bandwidth of queue
	
		curl -X POST -d '{"port_name": "s1-eth1", "type": "linux-htb", "max_rate": “1000000”, “queues”: [{"min_rate": “800000"}, {“max_rate”: “500000"}, {"max_rate": “0"}]}' http://localhost:8080/qos/queue/0000000000000001
	
	6.Now you can use iperf to produce traffic and monitor the bandwidth,for example
	
		In node:h1
	
			iperf -s -u -i 1 -p 5001
	
		In node:h2
	
			iperf -c 10.0.0.1 -p 5001 -u -b 1M
