# TeliaProjekt
Projekt sisaldab:
1. person_manager/ – mis on Spring Bootis tehtud bckend (Java 21, Gradle)
2. person_manager_frontend/ – mis on Vue.js frontend
3. docker-compose.yml – mis on fail konteinerite käivitamiseks

Paigaldatud peab olema
1. Docker 
2. Docker Compse
3. Java 21

Käivitamine:
1. Kontrolli, et sul ei jookse midagi portidel 8080 ja 8081
    docker ps – näed kõiki kasutusel olevaid porte
    lsof -i :8080 ja lsof -i :8081 – näed porte 8080 ja 8081
2. Ehitame backendi
    Mine terminalis kausta personal_manager 
        cd person_manager
3. Ehita backend
    ./gradlew build
    See tekitab faili personal_manager/build/libs/person_manager-0.0.1-SNAPSHOT.jar
4. Paneme frontendi ja backendi tööle
    Mine terminalis projekti kausta
    Sisesta terminali docker-compose up –build
5. Rakenduse ligipääs
    Ava brauserist http://localhost:8081/ frontend.
    Soovi korral ava ka backend brauserist http://localhost:8080/api/persons
6. Kui soovid lõpetada
    Peata konteinerid
        docker-compose down
