run:docker network create mongo-cluster

then run:docker-compose up -d

then run: docker exec -it mongo1 mongosh


then run: rs.initiate(
  {
    _id: "rs0",
    members: [
      { _id: 0, host: "mongo1:27017" },
      { _id: 1, host: "mongo2:27017" },
      { _id: 2, host: "mongo3:27017" }
    ]
  }
)

rs.status() --> check status and primary database



.evn uri----- "mongodb://mongo1:27017,mongo2:27017,mongo3:27017/<db_name>?replicaSet=rs0"
compass-uri --- "mongodb://localhost:<primary-port>/<db_name>?replicaSet=rs0&directConnection=true"


note: need to enable network share

in each service yml
 networks:
      - mongo-cluster

in last in yml file
  networks:
    mongo-cluster:
      external: true
