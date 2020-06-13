pipeline {

    checkout scm

    docker.withRegistry('https://registry.hub.docker.com', 'dockerHub') {

        def customImage = docker.build("pascalegbenda/blue_image -f Blue-Green/Blue/Dockerfile Blue-Green/Blue")
        def customImage = docker.build("pascalegbenda/green_image -f Blue-Green/Green/Dockerfile Blue-Green/Green")

        /* Push the container to the custom Registry */
        customImage.push()
    }
}
