FROM centos:centos7
RUN yum install -y wget \
	&& wget http://nginx.org/download/nginx-1.18.0.tar.gz \
	&& tar -xzvf nginx-1.18.0.tar.gz
RUN yum install -y git \
	&& git clone https://github.com/chobits/ngx_http_proxy_connect_module.git
RUN yum install -y patch \
	&& cd nginx-1.18.0 \
	&& patch -p1 < /ngx_http_proxy_connect_module/patch/proxy_connect_rewrite_1018.patch
RUN yum install -y gcc pcre pcre-devel openssl openssl-devel gd gd-devel
WORKDIR /nginx-1.18.0
RUN  ./configure \
#	--with-http_ssl_module \
#	--with-http_v2_module \
	--with-http_stub_status_module \
	--add-module=/ngx_http_proxy_connect_module
RUN make && make install
RUN alias nginx=/usr/local/nginx/sbin/nginx
RUN mkdir /usr/local/nginx/conf/conf.d && mkdir /docker-entrypoint.d
COPY docker-entrypoint.sh /
COPY 10-listen-on-ipv6-by-default.sh /docker-entrypoint.d
COPY 20-envsubst-on-templates.sh /docker-entrypoint.d
ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 80

STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]

## If proxy pod would runs on Docker, comment in 2 lines below.
## If proxy pod would runs on Kubernetes, keep 2 lines below commented out due to using ConfigMap.

COPY ./conf.d/default.conf /usr/local/nginx/conf/conf.d/default.conf
COPY ./nginx/nginx.conf /usr/local/nginx/conf/nginx.conf

CMD ["/usr/local/nginx/sbin/nginx","-g", "daemon off;"]
