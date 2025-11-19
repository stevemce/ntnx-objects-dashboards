# objects-dashboards

# Introduction
The Nutanix Objects Prometheus Exporter provides a solution for Objects observability. The grafana dashboards, where the stats can be observed, will be connected to a prometheus server, which is configured to talk to the exporter periodically and dump the stats into prometheus. 

The exporter can be used to monitor the following stats:

   1. Object store and bucket level stats 
   2. Performance stats (individual bucket level as well as cumulative object store level): Total requests/sec, GET/sec, PUT/sec, throughput etc. 
 
# Prerequisites
An object store with a version >=3.5.1.

1. Prometheus instance installed on any machine. This instance will be configured to talk to the exporter and will store all the exported stats.
2. Grafana instance installed on any machine. This instance will be configured to talk to the prometheus instance and will show the exported stats through dashboards.
3. Prism Central, where the objects service is enabled and object stores deployed. The stats will be exported from here.

# Configuring Prometheus

1. Follow these steps if you do not have an existing Prometheus instance 
   a. You need to download and install the prometheus instance on a machine. Refer to this link for the steps.
	https://prometheus.io/docs/prometheus/latest/installation/
   b. After installation of Prometheus, check if you can access the Prometheus home page using the following link. If you are able to successfully access the Prometheus UI page, you can proceed with the next steps: http://<prometheus-vm-ip>:9090/

3. Now let’s configure the prometheus yaml configuration file, so that the Prometheus server can talk to the exporter and export stats. Go to the location /etc/prometheus/prometheus.yml inside the machine where prometheus is installed.
	cd /etc/prometheus/prometheus.yml
 
4. You can configure two types of endpoints for exporting the stats through the exporter. The steps are listed below. You need to provide the machine IP where the exporter would be running as the endpoint base url :

5. For monitoring performance, resource and other object store level stats for all the object stores deployed on a Prism Central, add the following in the yaml file

				- job_name: <job-name>
				   metrics_path: /oss/api/nutanix/metrics
				   scheme: https
				   scrape_interval: 90s
				   static_configs:
				   - targets: ['<pc-ip>:9440']
				   tls_config:
				     insecure_skip_verify: true
				   basic_auth:
				     username: <pc-username>
				     password: <pc-password>


6. For monitoring performance stats of a particular bucket of an object store managed by a Prism Central, add the following in the yaml file. 
Note: The object store and the bucket name can be fetched from the Prism Central UI.
			
				
				-  job_name: <job-name>
				   metrics_path: /oss/api/nutanix/metrics/<objectstore-name>/<bucket-name>	
				   scheme: https	
				   scrape_interval: 90s
				   static_configs:
				   - targets: ['<pc-ip>:9440']
				   tls_config:
				    insecure_skip_verify: true
				   basic_auth:
				     username: <pc-username>
				     password: <pc-password>

 
7. After making changes in the yaml you need to restart the prometheus server.


# Configuring Grafana

1. Follow these steps if you do not have an existing Grafana instance 
	
   a. You need to download and install the grafana instance on a machine. Refer to this link for the steps:
   https://grafana.com/docs/grafana/latest/installation/

   b. After installation of Grafana, check if you can access the Grafana page using the following link. If you are able to successfully access the Grafana UI page, you can proceed with the next steps: ttp://<grafana-vm-ip>:3000/

2. Now we need to configure Grafana to query the Prometheus server. Use the following steps: 

	a. Click on the datasource from the settings dropdown on the left vertical panel of grafana home page
	b. Click on the Add Data Source tab.
	c. Select Prometheus as Time Series Database, from where stats will be show
	d. Add the prometheus url (http://<prometheus-vm-ip>:9090) inside the url field. Grafana and Prometheus can be installed on the same VM/host
	e. Click Save & Test. Data source will be added if the url is correct.

 
# Create Dashboards 

Downaload each JSON file present in grafana_dashboards folder in this repository, and import each one into Grafana as a new dashboard. The dashboards contain collections of graphs showing the various stats that relate to that particular dahsboard category.

1. Click the "+" icon at the top right of the Grafana page
2. Select "Import dashboard" and navigate to the required dashboard json file. Select the Prometheus datasource you configured.  Also change the UID (unique identity), if the same UID is already present. UID can be any random alphanumeric character of length greater than 1 and must be unique. Your dashboard will then be added. You can go inside a particular stats and check the metrics related information such as name, values, labels etc.
