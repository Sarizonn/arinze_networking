FROM nginx:alpine

# Copy index.html and all necessary asset folders
# The source path '.' refers to the build context (/home/ec2-user/webpage)
COPY index.html /usr/share/nginx/html/
COPY css /usr/share/nginx/html/css/
COPY fonts/ /usr/share/nginx/html/fonts/
COPY images/ /usr/share/nginx/html/images/
COPY js/ /usr/share/nginx/html/js/
# Add any other static folders here

# Expose port 80 (optional — docker compose already maps ports)
EXPOSE 80