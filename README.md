# szaklon-docker-containers

### Stworzenie sieci kontenerów

Pierwszym krokiem jest stworzenie sieci kontenerów o nazwie _testnetwork_:

`docker network create testnetwork`

### Budowanie obrazu populatora

Na początku należy otworzyć plik _application.yml_ (zlokalizowany pod ścieżką: populator/src/main/resources/application.yml) i zmienić 6 linię na:

`url: jdbc:mysql://mysql-docker-container:3306?useUnicode=yes&characterEncoding=UTF-8&useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true`

W tym samym pliku należy ustawić adres extractora (ostatnia linia pliku) na:

`address: http://extractor-docker-container:5000`

Następnie, będąc w folderze /populator, należy wywołać poniższą komendę do zbudowania projektu:

`mvn clean install`

Po poprawnym utworzeniu pliku jar konieczne jest stworzenie pliku _Dockerfile_ (w folderze /populator) i przekopiowanie do niego zawartości pliku _Dockerfile_populator_, który znajduje się w tym repozytorium. Obraz kontenera populatora jest budowany poniższym poleceniem:

`docker build -f Dockerfile -t populator-docker-image .`

### Budowanie obrazu extractora

Należy przejść do folderu /extractor i umieścić w nim plik _Dockerfile_ (zawartość przekopiować z pliku _Dockerfile_extractor_ z tego repozytorium). Do zbudowania obrazu kontenera ekstraktora należy wywołać poniższą komendę:

`docker build -f Dockerfile -t extractor-docker-image .`

### Uruchamianie kontenerów

Uruchomienie kontenera **redisa**:

`docker run -d --name redis-docker-container -p 6379:6379 --network testnetwork redis`

Uruchomienie kontenera **mysql**:

`docker run -d -p 2012:3306 --name mysql-docker-container -e MYSQL_ROOT_PASSWORD=root12345 -e MYSQL_DATABASE=populator --network testnetwork mysql:latest`

Uruchomienie kontenera **extractora**:

`docker run -p 5000:5000 --name extractor-docker-container --network testnetwork extractor-docker-image`

Uruchomienie kontenera **populatora**:

`docker run -p 8080:8080 --name populator-docker-container --network testnetwork populator-docker-image`
