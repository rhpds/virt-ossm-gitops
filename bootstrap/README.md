## Podman Quadlet info

Here is an example Quadlet file:

```sh
cat $HOME/.config/containers/systemd/mysleep.container

[Unit]
Description=The sleep container
After=local-fs.target

[Container]
Image=registry.access.redhat.com/ubi9-minimal:latest
Exec=sleep 1000

[Install]
# Start by default on boot
WantedBy=multi-user.target default.target
```

Put the Quadlet file for rootful containers here:

````
/usr/share/containers/systemd/

/etc/containers/systemd/
````

Put the Quadlet file for rootless containers here:

```sh
$HOME/.config/containers/systemd/
```

Reload systemd daemon (this triggers the generation of the systemd unit file from the Quadlet) and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl start mysql-db.service

sudo systemctl status mysql-db.service
```

You can validate the generation of the systemd unit file with this command:

```bash
/usr/libexec/podman/quadlet -dryrun -user
```

## Installing the VMs manually

```
oc apply -f applications/namespaces.yaml
```

## MYSQL DB

```
oc apply -f mysql-vm.yaml
oc apply -f mysql-svc.yaml 

# In VM Script
podman pull quay.io/kiali/demo_travels_mysqldb:v1
podman run --name mysql-container  -e MYSQL_ROOT_PASSWORD=mysqldbpass -d -p 3306:3306 quay.io/kiali/demo_travels_mysqldb:v1
```

## TRAVELS

```
oc apply -f travels-vm.yaml
oc apply -f flitravelsghts-svc.yaml 

# In VM Script
podman run -dt -p 8000:8000 \
--name travels-container \
-e CURRENT_SERVICE='travels'  \
-e  CURRENT_VERSION='v1' \
-e  LISTEN_ADDRESS=':8000' \
-e  FLIGHTS_SERVICE='http://fedora-flights.travel-agency.svc.cluster.local:8000' \
-e  HOTELS_SERVICE='http://fedora-hotels.travel-agency.svc.cluster.local:8000' \
-e  CARS_SERVICE='http://fedora-cars.travel-agency.svc.cluster.local:8000' \
-e  INSURANCES_SERVICE='http://fedora-insurances.travel-agency.svc.cluster.local:8000' \
quay.io/kiali/demo_travels_travels:v1
```

## FLIGHTS

```
oc apply -f flights-vm.yaml
oc apply -f flights-svc.yaml 

# In VM Script
podman run -dt -p 8000:8000 \
--name flights-container \
-e CURRENT_SERVICE='flights'  \
-e  CURRENT_VERSION='v1' \
-e  LISTEN_ADDRESS=':8000' \
-e  MYSQL_SERVICE='fedora-mysqldb:3306' \
-e  MYSQL_USER='root' \
-e  MYSQL_PASSWORD='mysqldbpass' \
-e  DISCOUNTS_SERVICE='http://fedora-discounts.travel-agency.svc.cluster.local:8000' \
-e  MYSQL_DATABASE='test' \
quay.io/kiali/demo_travels_flights:v1
```


## HOTELS

```
oc apply -f hotels-vm.yaml
oc apply -f hotels-svc.yaml 

# In VM Script
podman run -dt -p 8000:8000 \
--name hotels-container \
-e CURRENT_SERVICE='hotels'  \
-e  CURRENT_VERSION='v1' \
-e  LISTEN_ADDRESS=':8000' \
-e  MYSQL_SERVICE='fedora-mysqldb:3306' \
-e  MYSQL_USER='root' \
-e  MYSQL_PASSWORD='mysqldbpass' \
-e  DISCOUNTS_SERVICE='http://fedora-discounts.travel-agency.svc.cluster.local:8000' \
-e  MYSQL_DATABASE='test' \
quay.io/kiali/demo_travels_hotels:v1
```


## CARS

```
oc apply -f cars-vm.yaml
oc apply -f cars-svc.yaml 
# In VM Script
podman run -dt -p 8000:8000 \
--name cars-container \
-e CURRENT_SERVICE='cars'  \
-e  CURRENT_VERSION='v1' \
-e  LISTEN_ADDRESS=':8000' \
-e  MYSQL_SERVICE='fedora-mysqldb:3306' \
-e  MYSQL_USER='root' \
-e  MYSQL_PASSWORD='mysqldbpass' \
-e  DISCOUNTS_SERVICE='http://fedora-discounts.travel-agency.svc.cluster.local:8000' \
-e  MYSQL_DATABASE='test' \
quay.io/kiali/demo_travels_cars:v1
```


## INSURANCES

```
oc apply -f insurances-vm.yaml
oc apply -f insurances-svc.yaml 

# In VM Script
podman run -dt -p 8000:8000 \
--name insurances-container \
-e  CURRENT_SERVICE='insurances'  \
-e  CURRENT_VERSION='v1' \
-e  LISTEN_ADDRESS=':8000' \
-e  DISCOUNTS_SERVICE='http://fedora-discounts.travel-agency.svc.cluster.local:8000' \
-e  MYSQL_SERVICE='fedora-mysqldb:3306' \
-e  MYSQL_USER='root' \
-e  MYSQL_PASSWORD='mysqldbpass' \
-e  MYSQL_DATABASE='test' \
quay.io/kiali/demo_travels_insurances:v1
```

## DISCOUNTS

```
oc apply -f discounts-vm.yaml
oc apply -f discounts-svc.yaml 

# In VM Script
podman run -dt -p 8000:8000 \
--name discounts-container \
-e  CURRENT_SERVICE='discounts'  \
-e  CURRENT_VERSION='v1' \
-e  LISTEN_ADDRESS=':8000' \
quay.io/kiali/demo_travels_discounts:v1
```


## CONTROL PORTAL

```
oc apply -f control-vm.yaml
oc apply -f control-svc.yaml 

# In VM Script
podman run -dt -p 8080:8080 \
--name control-container \
-e  PORTAL_SERVICES='voyages.fr;http://voyages.travel-portal.svc.cluster.local:8000,viaggi.it;http://viaggi.travel-portal.svc.cluster.local:8000,travels.uk;http://travels.travel-portal.svc.cluster.local:8000' \
quay.io/kiali/demo_travels_control:v1
```
