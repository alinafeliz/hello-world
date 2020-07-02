package main {
    ctx := context.Background()
    cli, err := client.NewEnvClient()
    if err != nil {
        panic(err)
    }

    // Делаем docker pull
    reader, err := cli.ImagePull(ctx, "docker.io/library/alpine", types.ImagePullOptions{})
    if err != nil {
        panic(err)
    }
    io.Copy(os.Stdout, reader)

    hostBinding := nat.PortBinding{
        HostIP:   "0.0.0.0",
        HostPort: "8000",
    }
    containerPort, err := nat.NewPort("tcp", "80")
    if err != nil {
        panic("Unable to get the port")
    }
    portBinding := nat.PortMap{containerPort: []nat.PortBinding{hostBinding}}

    // Создаем контейнер с заданной конфигурацией
    resp, err := cli.ContainerCreate(ctx, &container.Config{
        Image: "alpine",
        Cmd:   []string{"echo", "hello world"},
        Tty:   true,
    }, &container.HostConfig{
        PortBindings: portBinding,
    }, nil, "")
    if err != nil {
        panic(err)
    }

    // Запускаем контейнер
    if err := cli.ContainerStart(ctx, resp.ID, types.ContainerStartOptions{}); err != nil {
        panic(err)
    }

    // Получаем логи контейнера
    out, err := cli.ContainerLogs(ctx, resp.ID, types.ContainerLogsOptions{ShowStdout: true})
    if err != nil {
        panic(err)
    }
    io.Copy(os.Stdout, out)
}
