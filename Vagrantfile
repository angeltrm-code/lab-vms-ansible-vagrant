# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Recomendacion: evita actualizaciones automaticas del guest para que el lab sea mas predecible.
  # config.vm.box_check_update = false

  # Red Host-Only (privada) para IPs fijas del laboratorio.
  # NAT se crea automaticamente como primera interfaz en VirtualBox.
  LAB_NET = "192.168.56."

  # =========================
  # VM: control (Debian)
  # =========================
  config.vm.define "control" do |control|
    control.vm.box = "debian/bookworm64"
    control.vm.hostname = "control"
    control.vm.network "private_network", ip: "#{LAB_NET}10"

    control.vm.provider "virtualbox" do |vb|
      vb.name = "lab-control"
      vb.cpus = 1
      vb.memory = 1536
    end
  end

  # =========================
  # VM: web-nginx (Debian)
  # =========================
  config.vm.define "web-nginx" do |web_nginx|
    web_nginx.vm.box = "debian/bookworm64"
    web_nginx.vm.hostname = "web-nginx"
    web_nginx.vm.network "private_network", ip: "#{LAB_NET}11"

    web_nginx.vm.provider "virtualbox" do |vb|
      vb.name = "lab-web-nginx"
      vb.cpus = 1
      vb.memory = 1024
    end
  end

  # =========================
  # VM: db-mariadb (Rocky)
  # =========================
  config.vm.define "db-mariadb" do |db_mariadb|
    db_mariadb.vm.box = "rockylinux/9"
    db_mariadb.vm.box_version = "5.0.0"
    db_mariadb.vm.hostname = "db-mariadb"
    db_mariadb.vm.network "private_network", ip: "#{LAB_NET}12"

    db_mariadb.vm.provider "virtualbox" do |vb|
      vb.name = "lab-db-mariadb"
      vb.cpus = 2
      vb.memory = 2048
    end
  end
end
