# go modbus
[![GoDoc](https://godoc.org/github.com/thinkgos/gomodbus?status.svg)](https://godoc.org/github.com/thinkgos/gomodbus)
[![Build Status](https://www.travis-ci.org/thinkgos/gomodbus.svg?branch=master)](https://www.travis-ci.org/thinkgos/gomodbus)
[![codecov](https://codecov.io/gh/thinkgos/gomodbus/branch/master/graph/badge.svg)](https://codecov.io/gh/thinkgos/gomodbus)
![Action Status](https://github.com/thinkgos/gomodbus/workflows/Go/badge.svg)
[![Go Report Card](https://goreportcard.com/badge/github.com/thinkgos/gomodbus)](https://goreportcard.com/report/github.com/thinkgos/gomodbus)
[![Licence](https://img.shields.io/github/license/thinkgos/gomodbus)](https://raw.githubusercontent.com/thinkgos/gomodbus/master/LICENSE)


### Supported formats

- modbus Serial(RTU,ASCII) Client
- modbus TCP Client
- modbus TCP Server

### Features

- object pool design,reduce memory allocation
- fast encode and decode
- interface design
- simple API and support raw data api

### Installation

Use go get.
```bash
    go get github.com/thinkgos/gomodbus/v2
```

Then import the modbus package into your own code.
```bash
    import modbus "github.com/thinkgos/gomodbus/v2"
```

### Supported functions

---

Bit access:
*   Read Discrete Inputs
*   Read Coils
*   Write Single Coil
*   Write Multiple Coils

16-bit access:
*   Read Input Registers
*   Read Holding Registers
*   Write Single Register
*   Write Multiple Registers
*   Read/Write Multiple Registers
*   Mask Write Register
*   Read FIFO Queue

### Example

---

```go
	p := modbus.NewTCPClientProvider("192.168.199.188:502",
		modbus.WithEnableLogger())
	client := modbus.NewClient(p)
	err := client.Connect()
	if err != nil {
		fmt.Println("connect failed, ", err)
		return
	}

	defer client.Close()
    fmt.Println("starting")
	for {
		results, err := client.ReadCoils(1, 0, 10)
		if err != nil {
			fmt.Println(err.Error())
		} else {
			fmt.Printf("ReadCoils % x", results)
		}
		time.Sleep(time.Second * 5)
	}
```

```go
    // modbus RTU/ASCII Client
	p := modbus.NewRTUClientProvider(modbus.WithEnableLogger(),
		modbus.WithSerialConfig(serial.Config{
			Address:  "COM5",
			BaudRate: 115200,
			DataBits: 8,
			StopBits: 1,
			Parity:   "N",
			Timeout:  modbus.SerialDefaultTimeout,
		}))
	client := modbus.NewClient(p)
	err := client.Connect()
	if err != nil {
		fmt.Println("connect failed, ", err)
		return
	}

	defer client.Close()
    fmt.Println("starting")
	for {
		results, err := client.ReadCoils(1, 0, 10)
		if err != nil {
			fmt.Println(err.Error())
		} else {
			fmt.Printf("ReadDiscreteInputs %#v\r\n", results)
		}
		time.Sleep(time.Second * 5)
	}
```

```go
    // modbus TCP Server
	srv := modbus.NewTCPServer(":502")
	srv.Logger = log.New(os.Stdout, "modbus", log.Ltime)
	srv.LogMode(true)

	srv.AddNode(modbus.NewNodeRegister(
		1,
		0, 10, 0, 10, 
		0, 10, 0, 10,
	))
	err := srv.ListenAndServe(":502")
	if err != nil {
		panic(err)
	}
```

### References

---

- [Modbus Specifications and Implementation Guides](http://www.modbus.org/specs.php)
- [goburrow](https://github.com/goburrow/modbus)