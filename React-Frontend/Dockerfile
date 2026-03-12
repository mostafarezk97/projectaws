# Use lightweight nginx
FROM nginx:alpine

# Remove default nginx config
RUN rm /etc/nginx/conf.d/default.conf

# Copy custom nginx config
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Copy React build output
COPY ./dist /usr/share/nginx/html

# Expose web port
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]