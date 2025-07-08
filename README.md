# pyralgen

`pyralgen` is a lightweight Python-based UVM-RAL code generator that leverages
[SystemRDL](https://www.accellera.org/downloads/standards/systemrdl) to describe
the registers and adds to [PeakRDL-uvm](https://github.com/SystemRDL/PeakRDL-uvm)
the register coverage functionality.

## Features
- Generates UVM-RAL code with coverage
- Easy to extend the given templates
- Following industry best practices

## Prerequisites

- Python 3.9.21 or later.

## Installing

Install using the given script because it's still under development:

```bash
./scripts/setup/setup_python_env.sh
```

## Example

The easiest way to use `pyralgen` is via the command line tool:

```bash
# Verify installation
pyralgen -h

# Generate UVM-RAL
pyralgen -c registers.rdl -o ral_regs_pkg.sv
```

## Integration

Inside the `top_env.sv` make sure you have the following:

```verilog
class top_env extends uvm_env;

  `uvm_component_utils(top_env)

  ...
  // MATT PROTOCOL UVC Register Model integration
  mattonella_reg_block                                 regmodel;
  matt_protocol_uvc_adapter                            m_reg2bus;
  uvm_reg_predictor #(matt_protocol_uvc_sequence_item) m_bus2reg_predictor;
  ...


  function void top_env::build_phase(uvm_phase phase);
    ...
    // MATT PROTOCOL UVC Register Model integration
    if (regmodel == null) begin
      // Enable Coverage models for register model
      uvm_reg::include_coverage("*", UVM_CVR_ALL);  // <- You need to add this line
      regmodel = mattonella_reg_block::type_id::create("regmodel", this);
      uvm_config_db #(mattonella_reg_block)::set(this, "", "regmodel", regmodel);
      regmodel.build();
      // Enable sampling of coverage
      if (m_config.coverage_enable) begin
        regmodel.set_coverage(UVM_CVR_ALL);     // <- You need also to add this
      end
      regmodel.lock_model();
      regmodel.reset()
    end
```

## Development

1. Clone de repository and navigate to its root directory:

    ```bash
    git clone https://github.com/cirofabianbermudez/pyralgen.git
    cd pyuvcgen
    ```

2. Create a Python virtual environment and install dependencies:

    ```bash
    ./script/setup/setup_python_env.sh
    source .venv/bin/activate
    ```

3. Verify installation:

    ```bash
    pyuvcgen -h
    ```

## License

TODO

## Contributing

Contributions, issues, and feature requests are welcome! Feel free to check issues or submit a pull request.
