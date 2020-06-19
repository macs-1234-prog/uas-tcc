# uas-tcc
Nama : Marcus Clementio Keleka Leton
Nim : 155610125
Kelas 1 sistem informasi / S1
Hari/tgl : jumad 19 Juni 2020
DOSEN : bambang 
1.Docker for Beginners - Linux.
Tutorial Docker untuk Pemula
Apa itu Docker?
DOCKER adalah perangkat lunak komputer yang digunakan untuk Virtualisasi agar beberapa sistem Operasi berjalan pada host yang sama. Tidak seperti Hypervisor yang digunakan untuk membuat VM (mesin Virtual), virtualisasi di Docker dilakukan pada tingkat sistem dalam apa yang disebut wadah Docker.

Seperti Anda dapat melihat perbedaan pada gambar di bawah, wadah Docker berjalan di atas sistem Operasi host. Ini membantu Anda meningkatkan efisiensi dan keamanan. Selain itu, kami dapat menjalankan lebih banyak kontainer pada infrastruktur yang sama daripada kami dapat menjalankan mesin Virtual karena kontainer menggunakan sumber daya yang lebih sedikit.
2.Docker Networking.
Buat jaringan

API 1.21+ Klien dan daemon API harus memiliki setidaknya 1.21 untuk menggunakan perintah ini. Gunakan perintah versi buruh pelabuhan pada klien untuk memeriksa klien Anda dan daemon versi API.

Pemakaian
docker network create [OPTIONS] NETWORK
Deskripsi yang diperluas🔗
Menciptakan jaringan baru. DRIVER menerima bridge atau overlay yang merupakan driver jaringan bawaan. Jika Anda telah menginstal pihak ketiga atau driver jaringan kustom Anda sendiri, Anda dapat menentukan DRIVER di sini juga. Jika Anda tidak menentukan opsi --driver, perintah secara otomatis membuat jaringan jembatan untuk Anda. Ketika Anda menginstal Docker Engine, ia menciptakan jaringan jembatan secara otomatis. Jaringan ini sesuai dengan jembatan docker0 yang secara tradisional diandalkan oleh Engine. Ketika Anda meluncurkan wadah baru dengan buruh pelabuhan menjalankannya secara otomatis terhubung ke jaringan jembatan ini. Anda tidak dapat menghapus jaringan jembatan default ini, tetapi Anda dapat membuat yang baru menggunakan perintah buat jaringan.
$ docker network create -d bridge my-bridge-network
Jaringan jembatan adalah jaringan yang terisolasi pada instalasi Engine tunggal. Jika Anda ingin membuat jaringan yang mencakup beberapa host Docker yang menjalankan Engine, Anda harus membuat jaringan overlay. Tidak seperti menjembatani jaringan, jaringan hamparan memerlukan beberapa kondisi yang sudah ada sebelum Anda dapat membuatnya. Kondisi-kondisi ini adalah:

Akses ke toko nilai kunci. Engine mendukung toko nilai kunci Consul, Etcd, dan ZooKeeper (Distributed store).
Sekelompok host dengan konektivitas ke penyimpanan nilai kunci.
Daemon Engine yang dikonfigurasikan dengan benar pada setiap host di cluster.
Opsi dockerd yang mendukung jaringan overlay adalah:
--cluster-store
--cluster-store-opt
--cluster-advertise
Untuk membaca lebih lanjut tentang opsi ini dan cara mengkonfigurasinya, lihat “Memulai dengan jaringan multi-host”.

Meskipun tidak diperlukan, sebaiknya pasang Docker Swarm untuk mengelola kluster yang membentuk jaringan Anda. Swarm menyediakan penemuan canggih dan alat manajemen server yang dapat membantu implementasi Anda.

Setelah Anda menyiapkan prasyarat jaringan overlay, Anda cukup memilih host Docker di cluster dan mengeluarkan yang berikut untuk membuat jaringan:
$ docker network create -d overlay my-multihost-network
Nama jaringan harus unik. Daemon Docker mencoba mengidentifikasi konflik penamaan tetapi ini tidak dijamin. Adalah tanggung jawab pengguna untuk menghindari konflik nama.

Batasi batasan jaringan
Anda harus membuat jaringan overlay dengan / 24 blok (default), yang membatasi Anda ke 256 alamat IP, ketika Anda membuat jaringan menggunakan mode endpoint berbasis VIP standar. Rekomendasi ini membahas batasan dengan mode swarm. Jika Anda membutuhkan lebih dari 256 alamat IP, jangan menambah ukuran blok IP. Anda dapat menggunakan mode titik akhir dnsrr dengan penyeimbang beban eksternal, atau menggunakan beberapa jaringan overlay yang lebih kecil. Lihat Mengkonfigurasi penemuan layanan untuk informasi lebih lanjut tentang berbagai mode titik akhir.

Sebagai contoh penggunaan perintah ini, lihat bagian contoh di bawah ini.
Options
Name, shorthand	Default	Description
--attachable		API 1.25+
Enable manual container attachment
--aux-address		Auxiliary IPv4 or IPv6 addresses used by Network driver
--config-from		API 1.30+
The network from which copying the configuration
--config-only		API 1.30+
Create a configuration only network
--driver , -d	bridge	Driver to manage the Network
--gateway		IPv4 or IPv6 Gateway for the master subnet
--ingress		API 1.29+
Create swarm routing-mesh network
--internal		Restrict external access to the network
--ip-range		Allocate container ip from a sub-range
--ipam-driver		IP Address Management Driver
--ipam-opt		Set IPAM driver specific options
--ipv6		Enable IPv6 networking
--label		Set metadata on a network
--opt , -o		Set driver specific options
--scope		API 1.30+
Control the network’s scope
--subnet		Subnet in CIDR format that represents a network segment
Examples
Connect containers
When you start a container, use the --network flag to connect it to a network. This example adds the busybox container to the mynet network:

$ docker run -itd --network=mynet busybox
If you want to add a container to a network after the container is already running, use the docker network connect subcommand.

You can connect multiple containers to the same network. Once connected, the containers can communicate using only another container’s IP address or name. For overlay networks or custom plugins that support multi-host connectivity, containers connected to the same multi-host network but launched from different Engines can also communicate in this way.

You can disconnect a container from a network using the docker network disconnect command.

Specify advanced options
When you create a network, Engine creates a non-overlapping subnetwork for the network by default. This subnetwork is not a subdivision of an existing network. It is purely for ip-addressing purposes. You can override this default and specify subnetwork values directly using the --subnet option. On a bridge network you can only create a single subnet:

$ docker network create --driver=bridge --subnet=192.168.0.0/16 br0
Additionally, you also specify the --gateway --ip-range and --aux-address options.

$ docker network create \
  --driver=bridge \
  --subnet=172.28.0.0/16 \
  --ip-range=172.28.5.0/24 \
  --gateway=172.28.5.254 \
  br0
If you omit the --gateway flag the Engine selects one for you from inside a preferred pool. For overlay networks and for network driver plugins that support it you can create multiple subnetworks. This example uses two /25 subnet mask to adhere to the current guidance of not having more than 256 IPs in a single overlay network. Each of the subnetworks has 126 usable addresses.

$ docker network create -d overlay \
  --subnet=192.168.10.0/25 \
  --subnet=192.168.20.0/25 \
  --gateway=192.168.10.100 \
  --gateway=192.168.20.100 \
  --aux-address="my-router=192.168.10.5" --aux-address="my-switch=192.168.10.6" \
  --aux-address="my-printer=192.168.20.5" --aux-address="my-nas=192.168.20.6" \
  my-multihost-network
Be sure that your subnetworks do not overlap. If they do, the network create fails and Engine returns an error.

Bridge driver options
When creating a custom network, the default network driver (i.e. bridge) has additional options that can be passed. The following are those options and the equivalent docker daemon flags used for docker0 bridge:

Option	Equivalent	Description
com.docker.network.bridge.name	-	Bridge name to be used when creating the Linux bridge
com.docker.network.bridge.enable_ip_masquerade	--ip-masq	Enable IP masquerading
com.docker.network.bridge.enable_icc	--icc	Enable or Disable Inter Container Connectivity
com.docker.network.bridge.host_binding_ipv4	--ip	Default IP when binding container ports
com.docker.network.driver.mtu	--mtu	Set the containers network MTU
com.docker.network.container_interface_prefix	-	Set a custom prefix for container interfaces
The following arguments can be passed to docker network create for any network driver, again with their approximate equivalents to docker daemon.

Argument	Equivalent	Description
--gateway	-	IPv4 or IPv6 Gateway for the master subnet
--ip-range	--fixed-cidr	Allocate IPs from a range
--internal	-	Restrict external access to the network
--ipv6	--ipv6	Enable IPv6 networking
--subnet	--bip	Subnet for network
For example, let’s use -o or --opt options to specify an IP address binding when publishing ports:

$ docker network create \
    -o "com.docker.network.bridge.host_binding_ipv4"="172.19.0.1" \
    simple-network
Network internal mode
By default, when you connect a container to an overlay network, Docker also connects a bridge network to it to provide external connectivity. If you want to create an externally isolated overlay network, you can specify the --internal option.

Network ingress mode
You can create the network which will be used to provide the routing-mesh in the swarm cluster. You do so by specifying --ingress when creating the network. Only one ingress network can be created at the time. The network can be removed only if no services depend on it. Any option available when creating an overlay network is also available when creating the ingress network, besides the --attachable option.

$ docker network create -d overlay \
  --subnet=10.11.0.0/16 \
  --ingress \
  --opt com.docker.network.driver.mtu=9216 \
  --opt encrypted=true \
  my-ingress-network
Parent command
Command	Description
docker network	Manage networks
Related commands
Command	Description
docker network connect	Connect a container to a network
docker network create	Create a network
docker network disconnect	Disconnect a container from a network
docker network inspect	Display detailed information on one or more networks
docker network ls	List networks
docker network prune	Remove all unused networks
docker network rm	Remove one or more networks
3.Application Containerization and Microservice Orchestration. Bagian ini menjelaskan tentang Docker Compose untuk App Containerization and Orchestration.
Docker Orchestration
kali ini saya akan membahas mengenai Docker. Memang sebelumnya sudah dijelaskan pada blog saya yang ini dan digabung dengan CI/CD. Jadi kali ini kita akan lebih membahas mengenai bagaimana cara kerja Docker image secara merinci.
In a normal virtualized environment, one or more virtual machines run on top of a physical machine using a hypervisor like Xen, Hyper-V etc. Containers, on the other hand, run in user space on top of operating systems kernel. It can be called as OS level virtualization.
Dari kutipan tersebut, dapat disimpulkan kesamaan dari VM dan container sebagai berikut.
Membutuhkan sistem host untuk berjalan
Memiliki isolasi terhadap proses, jaringan, user, dll
Bisa dikostumisasi sesuai dengan spesifikasi kebutuhan
Disimpan sebagai image
Kemudian untuk perbedaan dari VM dan container, yaitu:
Container biasanya hanya berisi kode aplikasi
Container ketika dijalankan hanya menjalankan proses yang dibutuhkan oleh container tersebut
Karena container tidak membutuhkan sistem operasi sendiri untuk berjalan, maka resource yang dibutuhkan lebih sedikit dibandingkan dengan VM

Microservices Architecture
Container sendiri mulai booming baru-baru ini dengan adanya konsep arsitektur microservice yang artinya suatu aplikasi dipecah menjadi service yang lebih kecil dan loosely coupled. Tujuan dipecahnya aplikasi tersebut adalah modularitas yang nantinya meudahkan ketika memahami struktur, pengembangan, dan testing. Untuk lebih lengkapnya dapat dilihat pada link berikut ini.
From Code to Containerized Running App

Docker
Tools container yang sering digunakan saat ini adalah Docker. Docker ini juga kami gunakan dalam mata kuliah PPL ini. Pada bagian ini saya akan menjelaskan mengenai bagaimana membuat kode menjadi containerized running app.
Pastikan membuat Dockerfile pada root directory dari project, jalankan Docker build beserta dengan nama image yang diinginkan
Untuk mengecek apakah aplikasi sudah bisa dijalankan pada Docker container, gunakan docker run <nama image>. Perintah ini berfungsi untuk menjalankan docker image pada container di local
Push Docker image ke Docker hub atau ke registry, pada PPL ini digunakan registry private dari PPL
Buat container pada portainer PPL, pull image dari registry PPL
Jalankan Docker image
Perintah untuk build dan push Docker image terdapat pada gitlab ci kelompok kami, lebih tepatnya pada bagian *_deploy. Berikut ini salah satu contohnya
webapp_dev_deploy:
  ...
  script:
    - docker build -f webapp/Dockerfile -t registry.docker.ppl.cs.ui.ac.id/ppld7/webapp-dev:latest .
    - docker push registry.docker.ppl.cs.ui.ac.id/ppld7/webapp-
Pada tag webapp_dev_deploy tersebut berfungsi untuk deployment saat proses development. Script berisi perintah untuk build image Docker dan push ke registry PPL Fasilkom, bukan Docker hub. Kemudian nantinya di Portainer, kita akan melakukan pull image dari registry Fasilkom dan run image tersebut. Berikut ini kira-kira tampilan Portainer.

Sekian penjelasan dari saya mengenai Docker, semoga dapat mencerahkan pemahaman anda mengenai cara kerja Docker. 
4.Docker Orchestration Hands-on Lab.
Laboratorium Praktek Orkestrasi Docker
20 Jan 2017

Di lab ini Anda akan bermain-main dengan fitur orkestrasi wadah Docker. Anda akan menggunakan aplikasi sederhana ke satu host dan mempelajari cara kerjanya. Kemudian, Anda akan mengkonfigurasi Docker Swarm Mode, dan belajar untuk menggunakan aplikasi sederhana yang sama di beberapa host. Anda kemudian akan melihat bagaimana skala aplikasi dan memindahkan beban kerja di host yang berbeda dengan mudah.

Kesulitan : Pemula

Waktu : Sekitar 30 menit

Tugas :

Bagian # 1 - Apa itu Orkestrasi
Bagian # 2 - Mengkonfigurasi Mode Kawanan
Bagian # 3 - Menyebarkan aplikasi di beberapa host
Bagian # 4 - Skala aplikasi
Bagian # 5 - Kuras simpul dan jadwalkan ulang wadah
Membersihkan
Bagian 1: Apa itu Orkestrasi
Jadi, apa itu orkestrasi? Yah, orkestrasi mungkin paling baik digambarkan menggunakan contoh. Katakanlah Anda memiliki aplikasi yang memiliki lalu lintas tinggi bersama dengan persyaratan ketersediaan tinggi. Karena persyaratan ini, Anda biasanya ingin menggunakan setidaknya 3+ mesin, sehingga jika tuan rumah gagal, aplikasi Anda masih dapat diakses dari setidaknya dua lainnya. Jelas, ini hanya sebuah contoh dan kasus penggunaan Anda kemungkinan akan memiliki persyaratan sendiri, tetapi Anda mendapatkan idenya.

Menyebarkan aplikasi Anda tanpa Orkestrasi biasanya sangat memakan waktu dan rawan kesalahan, karena Anda harus secara manual SSH ke setiap mesin, mulai aplikasi Anda, dan kemudian terus mengawasi hal-hal untuk memastikan itu berjalan seperti yang Anda harapkan.

Tetapi, dengan Perkakas orkestrasi, Anda biasanya dapat melepas sebagian besar pekerjaan manual ini dan membiarkan otomatisasi melakukan pekerjaan berat. Salah satu fitur keren Orkestrasi dengan Docker Swarm, adalah Anda dapat menggunakan aplikasi di banyak host dengan hanya satu perintah (setelah mode Swarm diaktifkan). Plus, jika salah satu node pendukung mati di Docker Swarm Anda, node lain akan secara otomatis mengambil beban, dan aplikasi Anda akan terus bersenandung seperti biasa.

Jika Anda biasanya hanya menggunakan docker run untuk menyebarkan aplikasi Anda, maka kemungkinan besar Anda akan mendapat manfaat dari menggunakan Docker Compose, Docker Swarm mode, atau keduanya Docker Compose dan Swarm.

Bagian 2: Konfigurasikan Mode Gerombolan
Aplikasi dunia nyata biasanya digunakan di beberapa host seperti yang dibahas sebelumnya. Ini meningkatkan kinerja aplikasi dan ketersediaan, serta memungkinkan komponen aplikasi individu untuk skala secara mandiri. Docker memiliki alat asli yang tangguh untuk membantu Anda melakukan ini.

Contoh menjalankan berbagai hal secara manual dan pada satu host adalah membuat wadah baru pada node1 dengan menjalankan docker run -dt ubuntu sleep infinity .

 docker run -dt ubuntu sleep infinity 
 Unable to find image 'ubuntu:latest' locally latest: Pulling from library/ubuntu d54efb8db41d: Pull complete f8b845f45a87: Pull complete e8db7bf7c39f: Pull complete 9654c40e9079: Pull complete 6d9ef359eaaa: Pull complete Digest: sha256:dd7808d8792c9841d0b460122f1acf0a2dd1f56404f8d1e56298048885e45535 Status: Downloaded newer image for ubuntu:latest 846af8479944d406843c90a39cba68373c619d1feaa932719260a5f5afddbf71 
Perintah ini akan membuat wadah baru berdasarkan ubuntu:latest gambar ubuntu:latest dan akan menjalankan perintah sleep untuk menjaga wadah tetap berjalan di latar belakang. Anda dapat memverifikasi wadah contoh kami sudah habis dengan menjalankan docker ps pada node1 .

 docker ps 
 CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES 044bea1c2277 ubuntu "sleep infinity" 2 seconds ago Up 1 second distracted_mayer 
Tapi, ini hanya pada satu node. Apa yang terjadi jika simpul ini turun? Nah, aplikasi kita baru saja mati dan tidak pernah restart. Untuk memulihkan layanan, kita harus masuk secara manual ke mesin ini, dan mulai mengubah hal-hal untuk membuatnya kembali dan berjalan. Jadi, akan sangat membantu jika kita memiliki beberapa jenis sistem yang memungkinkan kita untuk menjalankan aplikasi / layanan "tidur" ini di banyak mesin.

Di bagian ini Anda akan mengkonfigurasi Mode Swarm . Ini adalah mode opsional baru di mana beberapa host Docker membentuk kelompok mesin yang mengatur sendiri disebut swarm . Mode Swarm memungkinkan fitur baru seperti layanan dan bundel yang membantu Anda menyebarkan dan mengelola aplikasi multi-kontainer di beberapa host Docker.

Anda akan menyelesaikan yang berikut ini:

Konfigurasikan mode Swarm
Jalankan aplikasi
Skala aplikasi
Kuras node untuk perawatan dan penjadwalan ulang kontainer
Untuk sisa lab ini kita akan merujuk ke pengelompokan asli Docker sebagai mode Swarm . Koleksi mesin Docker yang dikonfigurasi untuk mode Swarm akan disebut sebagai swarm .

Segerombolan terdiri dari satu atau lebih Node Manajer dan satu atau lebih Node Pekerja . Node manajer menjaga kondisi gerombolan dan menjadwalkan wadah aplikasi. Node pekerja menjalankan wadah aplikasi. Pada Docker 1.12, tidak ada backend eksternal, atau komponen pihak ke-3, yang diperlukan untuk segerombolan yang berfungsi penuh - semuanya built-in!

Di bagian demo ini Anda akan menggunakan ketiga node di lab Anda. node1 akan menjadi manajer Swarm, sedangkan node2 dan node3 akan menjadi node pekerja. Mode Swarm mendukung node manajer redundan yang sangat tersedia, tetapi untuk keperluan lab ini Anda hanya akan menggunakan node manajer tunggal.

Langkah 2.1 - Buat simpul Manajer
Pada langkah ini Anda akan menginisialisasi Swarm baru, bergabung dengan node pekerja tunggal, dan memverifikasi operasi berhasil.

Jalankan docker swarm init pada node1 .

 docker swarm init --advertise-addr $(hostname -i) 
 Swarm initialized: current node (6dlewb50pj2y66q4zi3egnwbi) is now a manager. To add a worker to this swarm, run the following command: docker swarm join \ --token SWMTKN-1-1wxyoueqgpcrc4xk2t3ec7n1poy75g4kowmwz64p7ulqx611ih-68pazn0mj8p4p4lnuf4ctp8xy \ 10.0.0.5:2377 To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions. 
Anda dapat menjalankan perintah docker info untuk memverifikasi bahwa node1 berhasil dikonfigurasi sebagai node swarm manager.

 docker info 
 Containers: 2 Running: 0 Paused: 0 Stopped: 2 Images: 2 Server Version: 17.03.1-ee-3 Storage Driver: aufs Root Dir: /var/lib/docker/aufs Backing Filesystem: extfs Dirs: 13 Dirperm1 Supported: true Logging Driver: json-file Cgroup Driver: cgroupfs Plugins: Volume: local Network: bridge host macvlan null overlay Swarm: active NodeID: rwezvezez3bg1kqg0y0f4ju22 Is Manager: true ClusterID: qccn5eanox0uctyj6xtfvesy2 Managers: 1 Nodes: 1 Orchestration: Task History Retention Limit: 5 Raft: Snapshot Interval: 10000 Number of Old Snapshots to Retain: 0 Heartbeat Tick: 1 Election Tick: 3 Dispatcher: Heartbeat Period: 5 seconds CA Configuration: Expiry Duration: 3 months Node Address: 10.0.0.5 Manager Addresses: 10.0.0.5:2377 <Snip> 
Kawanan sekarang diinisialisasi dengan simpul1 sebagai satu-satunya simpul Manajer. Di bagian selanjutnya Anda akan menambahkan node2 dan node3 sebagai node Worker .

Langkah 2.2 - Bergabung dengan node Pekerja ke Swarm
Anda akan melakukan prosedur berikut pada node2 dan node3 . Menjelang akhir prosedur, Anda akan beralih kembali ke node1 .

Sekarang, ambil seluruh docker swarm join ... perintah kami salin sebelumnya dari node1 mana ia ditampilkan sebagai terminal output. Kita perlu menempelkan perintah yang disalin ke terminal node2 dan node3 .

Seharusnya terlihat seperti ini untuk node2 . Ngomong-ngomong, jika docker swarm join ... perintah docker swarm join ... menggulir layar Anda, Anda dapat menjalankan docker swarm join-token worker perintah pada node Manajer untuk mendapatkannya lagi.

Ingat, token yang ditampilkan di sini bukan token yang sebenarnya akan Anda gunakan. Salin perintah dari output pada node1 . Pada node2 dan node3 akan terlihat seperti ini:

 docker swarm join \ --token SWMTKN-1-1wxyoueqgpcrc4xk2t3ec7n1poy75g4kowmwz64p7ulqx611ih-68pazn0mj8p4p4lnuf4ctp8xy \ 10.0.0.5:2377 
 docker swarm join \ --token SWMTKN-1-1wxyoueqgpcrc4xk2t3ec7n1poy75g4kowmwz64p7ulqx611ih-68pazn0mj8p4p4lnuf4ctp8xy \ 10.0.0.5:2377 
Setelah Anda menjalankan ini pada node2 dan node3 , beralih kembali ke node1 , dan jalankan docker node ls untuk memverifikasi bahwa kedua node adalah bagian dari Swarm. Anda akan melihat tiga simpul, simpul 1 sebagai simpul Manajer dan simpul2 dan simpul3 sebagai simpul Pekerja.

 docker node ls 
 ID HOSTNAME STATUS AVAILABILITY MANAGER STATUS 6dlewb50pj2y66q4zi3egnwbi * node1 Ready Active Leader ym6sdzrcm08s6ohqmjx9mk3dv node3 Ready Active yu3hbegvwsdpy9esh9t2lr431 node2 Ready Active 
Perintah docker node ls menunjukkan kepada Anda semua node yang ada di swarm serta perannya di swarm. The * mengidentifikasi node dari mana Anda mengeluarkan perintah.

Selamat! Anda telah mengonfigurasi segerombolan dengan satu simpul manajer dan dua simpul pekerja.

Bagian 3: Menyebarkan aplikasi di beberapa host
Sekarang setelah Anda memiliki swarm up dan running, sekarang saatnya untuk menggunakan aplikasi tidur kami yang sangat sederhana.

Anda akan melakukan prosedur berikut dari node1 .

Langkah 3.1 - Menyebarkan komponen aplikasi sebagai layanan Docker
Aplikasi sleep kami menjadi sangat populer di internet (karena mengenai Reddit dan HN). Orang suka itu. Jadi, Anda harus mengukur aplikasi Anda untuk memenuhi permintaan puncak. Anda harus melakukan ini di beberapa host untuk ketersediaan tinggi juga. Kami akan menggunakan konsep Layanan untuk mengukur aplikasi kami dengan mudah dan mengelola banyak wadah sebagai satu kesatuan.

Layanan adalah konsep baru di Docker 1.12. Mereka bekerja dengan kawanan dan dimaksudkan untuk wadah yang sudah berjalan lama.

Anda akan melakukan prosedur ini dari node1 .

Mari kita sebarkan sleep sebagai Layanan di Docker Swarm kami.

 docker service create --name sleep-app ubuntu sleep infinity 
 of5rxsxsmm3asx53dqcq0o29c 
Verifikasi bahwa pembuatan service create telah diterima oleh manajer Swarm.

 docker service ls 
 ID NAME MODE REPLICAS IMAGE of5rxsxsmm3a sleep-app replicated 1/1 ubuntu:latest 
Status layanan dapat berubah beberapa kali hingga berjalan. Gambar sedang diunduh dari Docker Store ke mesin lain di Swarm. Setelah gambar diunduh, wadah masuk ke keadaan berjalan di salah satu dari tiga node.

Pada titik ini mungkin tidak terlihat bahwa kami telah melakukan sesuatu yang sangat berbeda dari hanya menjalankan docker run ... . Kami sekali lagi menggunakan satu wadah pada satu host. Perbedaannya di sini adalah bahwa wadah telah dijadwalkan pada gugus gerombolan.

Sudah selesai dilakukan dengan baik. Anda telah menyebarkan aplikasi tidur ke Swarm baru Anda menggunakan layanan Docker.

Bagian 4: Skala aplikasi
Permintaannya gila! Semua orang menyukai aplikasi sleep Anda! Sudah waktunya untuk meningkatkan skala.

Salah satu hal hebat tentang layanan adalah Anda dapat menaikkan dan menurunkannya untuk memenuhi permintaan. Pada langkah ini Anda akan meningkatkan skala layanan dan kemudian kembali.

Anda akan melakukan prosedur berikut dari node1 .

Skala jumlah kontainer dalam layanan sleep-app ke 7 dengan docker service update --replicas 7 sleep-app perintah docker service update --replicas 7 sleep-app . replicas adalah istilah yang kami gunakan untuk menggambarkan wadah identik yang menyediakan layanan yang sama.

 docker service update --replicas 7 sleep-app 
Manajer Swarm menjadwalkan sehingga ada 7 wadah sleep-app di kluster. Ini akan dijadwalkan secara merata di seluruh anggota Swarm.

Kita akan menggunakan perintah docker service ps sleep-app . Jika Anda melakukan ini cukup cepat setelah menggunakan opsi --replicas Anda dapat melihat wadah muncul secara real time.

 docker service ps sleep-app 
 ID NAME IMAGE NODE DESIRED STATE CURRENT STATE ERROR PORTS 7k0flfh2wpt1 sleep-app.1 ubuntu:latest node1 Running Running 9 minutes ago wol6bzq7xf0v sleep-app.2 ubuntu:latest node3 Running Running 2 minutes ago id50tzzk1qbm sleep-app.3 ubuntu:latest node2 Running Running 2 minutes ago ozj2itmio16q sleep-app.4 ubuntu:latest node3 Running Running 2 minutes ago o4rk5aiely2o sleep-app.5 ubuntu:latest node2 Running Running 2 minutes ago 35t0eamu0rue sleep-app.6 ubuntu:latest node2 Running Running 2 minutes ago 44s8d59vr4a8 sleep-app.7 ubuntu:latest node1 Running Running 2 minutes ago 
Perhatikan bahwa sekarang ada 7 kontainer yang terdaftar. Mungkin diperlukan beberapa detik untuk wadah baru dalam layanan untuk semua ditampilkan sebagai MENJALANKAN . Kolom NODE memberitahu kita di mana node wadah sedang berjalan.

Skala layanan kembali menjadi hanya empat kontainer dengan docker service update --replicas 4 sleep-app perintah docker service update --replicas 4 sleep-app .

 docker service update --replicas 4 sleep-app 
Verifikasi bahwa jumlah kontainer telah dikurangi menjadi 4 menggunakan perintah docker service ps sleep-app .

 docker service ps sleep-app 
 ID NAME IMAGE NODE DESIRED STATE CURRENT STATE ERROR PORTS 7k0flfh2wpt1 sleep-app.1 ubuntu:latest node1 Running Running 13 minutes ago wol6bzq7xf0v sleep-app.2 ubuntu:latest node3 Running Running 5 minutes ago 35t0eamu0rue sleep-app.6 ubuntu:latest node2 Running Running 5 minutes ago 44s8d59vr4a8 sleep-app.7 ubuntu:latest node1 Running Running 5 minutes ago 
Anda telah berhasil meningkatkan layanan gerombolan ke atas dan ke bawah.

Bagian 5: Kuras simpul dan jadwalkan ulang wadah
Aplikasi tidur Anda telah melakukan yang luar biasa setelah mencapai Reddit dan HN. Sekarang nomor 1 di App Store! Anda telah meningkatkan selama liburan dan turun selama musim yang lambat. Sekarang Anda sedang melakukan pemeliharaan di salah satu server Anda sehingga Anda harus dengan anggun mengeluarkan server dari kerumunan tanpa mengganggu layanan kepada pelanggan Anda.

Lihatlah status node Anda lagi dengan menjalankan docker node ls pada node1 .

 docker node ls 
 ID HOSTNAME STATUS AVAILABILITY MANAGER STATUS 6dlewb50pj2y66q4zi3egnwbi * node1 Ready Active Leader ym6sdzrcm08s6ohqmjx9mk3dv node3 Ready Active yu3hbegvwsdpy9esh9t2lr431 node2 Ready Active 
Anda akan mengambil node2 dari layanan untuk pemeliharaan.

Mari kita lihat wadah yang Anda jalankan di node2 .

 docker ps 
 CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES 4e7ea1154ea4 ubuntu@sha256:dd7808d8792c9841d0b460122f1acf0a2dd1f56404f8d1e56298048885e45535 "sleep infinity" 9 minutes ago Up 9 minutes sleep-app.6.35t0eamu0rueeozz0pj2xaesi 
Anda dapat melihat bahwa kami memiliki salah satu wadah aplikasi slepp berjalan di sini (output Anda mungkin terlihat berbeda).

Sekarang mari kita kembali ke node1 (manajer Swarm) dan mengambil node2 dari layanan. Untuk melakukan itu, mari kita jalankan lagi docker node ls .

 docker node ls 
 ID HOSTNAME STATUS AVAILABILITY MANAGER STATUS 6dlewb50pj2y66q4zi3egnwbi * node1 Ready Active Leader ym6sdzrcm08s6ohqmjx9mk3dv node3 Ready Active yu3hbegvwsdpy9esh9t2lr431 node2 Ready Active 
Kami akan mengambil ID untuk node2 dan menjalankan docker node update --availability drain yournodeid . Kami menggunakan ID host simpul2 sebagai input ke perintah drain kami. Ganti yournodeid dengan id dari node2 .

 docker node update --availability drain yournodeid 
Periksa status node

 docker node ls 
 ID HOSTNAME STATUS AVAILABILITY MANAGER STATUS 6dlewb50pj2y66q4zi3egnwbi * node1 Ready Active Leader ym6sdzrcm08s6ohqmjx9mk3dv node3 Ready Active yu3hbegvwsdpy9esh9t2lr431 node2 Ready Drain 
Node simpul2 sekarang dalam keadaan Drain .

Beralih kembali ke node2 dan lihat apa yang sedang berjalan di sana dengan menjalankan docker ps .

 docker ps 
 CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES 
node2 tidak memiliki kontainer yang berjalan di atasnya.

Terakhir, periksa lagi layanan pada node1 untuk memastikan bahwa kontainer dijadwal ulang. Anda harus melihat keempat kontainer berjalan di dua node yang tersisa.

 docker service ps sleep-app 
 ID NAME IMAGE NODE DESIRED STATE CURRENT STATE ERROR PORTS 7k0flfh2wpt1 sleep-app.1 ubuntu:latest node1 Running Running 25 minutes ago wol6bzq7xf0v sleep-app.2 ubuntu:latest node3 Running Running 18 minutes ago s3548wki7rlk sleep-app.6 ubuntu:latest node3 Running Running 3 minutes ago 35t0eamu0rue \_ sleep-app.6 ubuntu:latest node2 Shutdown Shutdown 3 minutes ago 44s8d59vr4a8 sleep-app.7 ubuntu:latest node1 Running Running 18 minutes ago 
Membersihkan
Jalankan perintah docker service rm sleep-app pada node1 untuk menghapus layanan yang disebut myservice .

 docker service rm sleep-app 
Jalankan perintah docker ps pada node1 untuk mendapatkan daftar kontainer yang berjalan.

 docker ps 
 CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES 044bea1c2277 ubuntu "sleep infinity" 17 minutes ago 17 minutes ag distracted_mayer 
Anda dapat menggunakan perintah docker kill <CONTAINER ID> pada node1 untuk membunuh wadah tidur yang kita mulai di awal.

 docker kill yourcontainerid 
Akhirnya, mari kita hapus node1, node2, dan node3 dari Swarm. Kita dapat menggunakan perintah docker swarm leave --force untuk melakukan itu.

Mari kita jalankan docker swarm leave --force on node1 .

 docker swarm leave --force 
Kemudian, jalankan docker swarm leave --force on node2 .

 docker swarm leave --force 
Akhirnya, jalankan docker swarm leave --force on node3 .

 docker swarm leave --force 
Selamat! Anda telah menyelesaikan lab ini. Anda sekarang tahu cara membangun segerombolan, menyebarkan aplikasi sebagai kumpulan layanan, dan meningkatkan skala layanan individual.

Bagikan ini di → https://twitter.com/docker https://www.facebook.com/docker.run Bagikan di Google+ LinkedIn
Jika baris perintah tidak muncul di terminal
5.Learn Kubertentes Basics. Praktikkan dan pahami tutorial interaktif yang ada disitu.

Edit This Page

Mempelajari Panduan Dasar Kubernetes
Panduan Dasar Kubernetes
Tutorial ini menyediakan panduan dasar mekanisme orkestrasi klaster Kubernetes. Setiap modul memliki beberapa informasi mengenai latar belakang bagi konsep mendasar dan feature Kubernetes, termasuk mode interaktif yang dapat digunakan sebagai metode pembelajaran online. Mode tutorial interaktif ini memberikan kesempatan pengguna untuk melakukan manajemen klaster sederhana beserta aplikasi dalam kontainer yang kamu miliki.

Dengan menggunakan mode tutorial interaktif ini, pengguna diharapkan dapat memahami:

Deployi> sebuah aplikasi yang sudah dikontainerisasi pada klaster
Melakukan scale deployment
Meng-update aplikasi yang sudah dikontainerisasi dengan menggunakan versi aplikasi terbaru
Men-debug aplikasi yang sudah dikontainerisasi
Tutorial ini menggunakan Katakoda untuk menjalankan terminal virtual diatas Minikube pada web browser kamu. Dengan demikian, kamu tidak perlu melakukan instalasi perangkat lunak apa pun, segala modul yang ada dijalankan secara langsung melalui web browser yang kamu miliki.


Apa yang dapat dilakukan oleh Kubernetes untuk kamu?
Seiring berkembangnya web servis modern, pengguna memiliki ekspektasi agar aplikasi selalu dapat diakses 24/7, selain itu developers juga memiliki harapan agar mereka dapat melakukan deployment aplikasi dengan versi terbaru yang mereka miliki berulang per hari. Mekanisme kontainerisasi mengepak perangkat lunak agar memenuhi kebutuhan yang ada, memudahkan serta mempercepat proses rilis dan pembaharuan aplikasi tanpa adanya downtime proses ini dapat dilakukan berulang per hari. Kubernetes membantu kamu menjaga aplikasi yang kamu buat untuk dapat dijalankan dimana pun dan kapan pun kamu menginginkannya, serta menjamin segala kebutuhan dan peralatan yang dibutuhkan oleh aplikasi kamu tersedia. Kubernetes merupakan platform open source yang sudah matang yang didesain berdasarkan pengalaman Google serta ide yang brilian dari komunitas yang ada.

Kubernetes Basics Modules

1. Create a Kubernetes cluster

2. Deploy an app

3. Explore your app
6.Kubernetes for Beginners.
Tutorial Pemula Untuk Kubernetes
Saya baru mengenal Kubernetes. Jadi ketika saya melihat di Meetup bahwa Weaveworks menjadi tuan rumah sesi Kelompok Pengguna yang memperkenalkan konsep penempatan wadah dan layanan mikro ke Kubernetes, saya pikir itu layak untuk dicoba. Saya senang saya melakukannya!
Hanya dalam waktu sekitar satu jam, Luke Marsden, yang mengepalai DX untuk Weaveworks, memberikan penjelasan rinci tentang konsep dan arsitektur utama Kubernetes, kemudian mendemonstrasikan semua itu dalam waktu nyata. Berikut adalah ringkasan tentang panduan konsep pemula Kubernetes, menjelaskan elemen apa, dan bagaimana semuanya cocok:

Apa itu Kubernet - definisi dan konsep utama
Berikut adalah visualisasi dari konsep-konsep Kubernet yang penting yang saya uraikan di bawah ini dari slide Luke:
Wadah
Wadah adalah unit terkecil di dunia Kubernetes. Tujuan utama Kubernetes adalah untuk mengelola, menyebarkan, dan, sampai batas tertentu, memantau wadah. Manajemen Kubernetes tidak terbatas pada wadah Docker.

Node
Node adalah host yang dijalankan wadah.

Polong
Pod adalah unit manajemen di Kubernetes yang terdiri dari satu atau lebih kontainer. Setiap pod memiliki alamat IP dan ruang penyimpanan yang unik. Semua kontainer berbagi sumber daya jaringan dan penyimpanan ini. Salah satu karakteristik yang disebutkan dalam presentasi ini adalah bahwa polong adalah "fana." Ini berarti ketika sebuah pod dihapus, ia pergi untuk selamanya. File YAML (Yet Another Markup Language) digunakan untuk mendefinisikan Pod. Berikut adalah contoh salinan file YAML yang mewakili Pod:
<code>
apiVersion: v1
kind: Pod
metadata:
 name: nginx-pod
 labels:
  app: nginx
spec:
 containers:
 - name: nginx
  image: nginx:1.7.9
</code>
Penyebaran
Penyebaran adalah cara baru untuk menangani Ketersediaan Tinggi (HA) di Kubernetes sebagai pengganti Pengontrol Replikasi. Pod dengan sendirinya adalah "fana" tetapi dengan Deployment, Kubernetes dapat memastikan bahwa jumlah Pod yang ditentukan pengguna selalu aktif dan berjalan di sistem. Penyebaran menentukan berapa banyak instance pod akan dijalankan. File YAML digunakan untuk mendefinisikan Penempatan. Berikut adalah contoh file YAML yang mewakili Penempatan:
<code>
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
 name: nginx-deployment
spec:
 replicas: 2
 template:
  metadata:
   labels:
    app: nginx
  spec:
   containers:
   - name: nginx
    image: nginx:1.7.9
</code>
Layanan
Menurut situs web resmi Kubernetes, "Layanan Kubernetes adalah abstraksi yang mendefinisikan serangkaian logis Pods dan kebijakan yang digunakan untuk mengaksesnya - kadang-kadang disebut layanan mikro."

Luke membahas konsep Cluster IP dan NodePorts. IP Cluster bersifat internal untuk Kubernetes, dan NodePort adalah alamat IP yang diterbitkan untuk pengguna eksternal untuk mengakses layanan. Rute ke layanan didasarkan pada label. Seperti halnya Pods and Deployments, layanan juga ditentukan oleh file YAML.

Diagram ini dari presentasi menempatkan file Layanan YAML dan file YAML Deployment berdampingan. Perhatikan panah yang memudahkan untuk melihat bahwa Layanan terhubung ke Penempatan melalui atribut label. Dalam hal ini, labelnya adalah nginx.

Catatan tambahan tentang “Deklaratif” di Kubernetes
Satu konsep Kubernet yang penting untuk diingat: “Deklaratif” adalah operasi berdasarkan file YAML. Kubernetes bergantung pada file YAML untuk memeriksa apakah Pod, Penempatan, atau Layanan yang diinginkan berjalan seperti yang ditentukan. Kita dapat melakukan kubectl menerapkan ratusan kali tanpa hasil yang tidak diinginkan atau tidak terduga. Kubernetes hanya memeriksa apakah sistem berjalan sesuai dengan kondisi yang diinginkan seperti yang didefinisikan dalam file YAML.
Untuk memberi Anda contoh, untuk skrip yang tidak deklaratif, ada perintah untuk menambahkan teks tertentu ke file. Itu hanya akan ditambahkan tanpa memeriksa apakah teks sudah ada di file itu. Jika Anda menerapkan skrip untuk menambahkan teks tertentu sepuluh kali, teks yang tepat akan muncul dalam file itu sepuluh kali.

Arsitektur Kubernetes
Karena batas waktu bicara, Luke berfokus pada simpul Master dan simpul pekerja dalam arsitektur Kubernetes. Node Master adalah bidang kontrol sedangkan node pekerja adalah tempat wadah digunakan.
Anda dapat menemukan deskripsi lebih rinci tentang arsitektur Kubernetes di GitHub.

Pada dasarnya, kita perlu menginisialisasi node Master dengan init kubeadm. Ini menciptakan beberapa sertifikat digital dan keluaran dengan perintah join kubeadm join –token =. Kita bisa menggunakan perintah itu pada node pekerja untuk bergabung ke master.

Untuk deskripsi yang lebih rinci tentang perintah kubeadm dan bagaimana menginisialisasi dan membuat berbagai node, lihat <Panduan Memulai Kubernetes untuk kubeadm.

Demo Langsung Jaringan Kubernetes
Untuk menunjukkan ini, Lukas hanya menggunakan satu master dan satu node pekerja.

Setelah master dan node pekerja diinisialisasi, kita perlu membuat jaringan. Luke menunjukkan bagaimana Weave Net membuat jaringan mendukung dengan mudah, sebuah proses yang sebaliknya akan mengambil banyak langkah lagi.

Untuk mempelajari lebih lanjut tentang jejaring Kubernetes, lihat dokumen Kubernetes.

Setelah Luke mengatur lingkungan Kubernetes, ia mendemonstrasikan penyebaran layanan. Menggunakan lingkungan Katacoda, ia membuat Pods dengan file YAML dan perintah kubectl apply. Dia juga menciptakan Layanan dengan menautkannya ke pod menggunakan konten file YAML.

Luke menunjukkan cara membuat dasbor langsung yang memvisualisasikan semua kegiatan dan potongan penyebaran, cara mudah untuk melacak kemajuan penyebaran. Dia melakukan ini menggunakan (bagian dari Weave Cloud).



Anda dapat mencoba komponen visualisasi Weave Cloud sendiri dengan mengikuti petunjuk langkah demi langkah yang ditemukan dalam Panduan Weaveworks. Untuk mencoba semua komponen Weave dalam satu komponen, Anda dapat mendaftar untuk Weave Cloud sebagai uji coba 60 hari gratis.

Anda juga bisa mendapatkan bantuan dengan bergabung dengan saluran slack Weaveworks atau dengan mengunjungi halaman bantuan komunitas Weaveworks.

Untuk sumber daya Kubernet yang lebih komprehensif, lihat:

Dokumentasi Resmi Kubernetes
Kubernetes Bootcamp
Kubernetes the Hard Way
Praktik Terbaik Kubernetes (The Google Way)
Berpisah Pikiran
Setelah pengantar singkat ini ke konsep-konsep kunci Kubernetes dan melihatnya langsung ditayangkan, saya ingin sekali dan bersemangat untuk mencoba Kubernetes. Menyaksikan Luke berjalan melalui langkah-langkah penyebaran membuatnya kurang menakutkan untuk mencobanya sendiri. Bagi siapa pun yang ingin pelatihan lebih dipandu untuk menggunakan Kubernet, Weaveworks juga menawarkan sesi pelatihan langsung kepada anggota Kelompok Pengguna. Anda dapat mengetahui tentang pembicaraan dan pelatihan yang akan datang dengan bergabung dengan Weave User Group. Jika Anda baru di Kubernetes seperti saya, atau memiliki pengalaman yang layak, Anda akan menemukan berbagai pembicaraan dan tingkat keahlian yang sangat berguna. Bergabunglah dengan Kelompok Pengguna Menenun.


