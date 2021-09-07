Однопоточный сервер постоянно ожидает клиентские подключения (accept_client_conn).

```python
def accept_client_conn(serv_sock, cid):
    client_sock, client_addr = serv_sock.accept()
    print(f'Client #{cid} connected '
          f'{client_addr[0]}:{client_addr[1]}')
    return client_sock
```

Получив новое соединение, сервер вычитывает запрос (read_request),

```python
def read_request(client_sock, delimiter=b'!'):
    request = bytearray()
    try:
        while True:
            chunk = client_sock.recv(4)
            if not chunk:
                # Клиент преждевременно отключился.
                return None

            request += chunk
            if delimiter in request:
                return request

    except ConnectionResetError:
        # Соединение было неожиданно разорвано.
        return None
    except:
        raise
```

обрабатывает его (handle_request)

```python
def handle_request(request):
    time.sleep(5)
    return request[::-1]
```

и отправляет клиенту ответ (write_response)

```python
def write_response(client_sock, response, cid):
    client_sock.sendall(response)
    client_sock.close()
    print(f'Client #{cid} has been served')
```

