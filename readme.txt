#create network
docker network create djan

#run database with name=database
docker run --name=database -e POSTGRES_PASSWORD=django -e POSTGRES_USER=django -e POSTGRES_DB=django --network djan -v /opt/database:/var/lib/postgresql/data -d -p 5432:5432 postgres:14.2

# build and run backend
docker build -t dj:prod .
docker run -d --network djan -p 8000:8000 dj:prod

#add data in postgres migrate db
docker exec -it <id backend_container> /bin/bash

python manage.py migrate
python manage.py loaddata bbk_data.json

#frontend start in djan network
docker build -t front:prod -f Dockerfile.prod .
docker run -d --network djan -p 3000:3000 front:prod