FROM almalinux:9
RUN dnf install nginx -y
RUN rm -rf /usr/shaer/nginx/html/index
COPY index.html /usr/shaer/nginx/html/index
CMD ["nginx","-g","daemon off;"]