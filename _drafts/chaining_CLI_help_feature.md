# Chaining CLI Help feature

We wish to implement a new Help feature, such that when a command line contains the flag `--help` the chaining CLI alters its normal behavior and  proceeds in _help-mode_.

In help-mode commands operate differently: normal execution of the `do_initializations` and `do_operation` methods is suppressed.  Instead, control flow proceeds as usual down the command chain until reaching the rightmost command, whereupon that command's `do_help` method is invoked.

To introduce the Help feature into the current code (as of 2025-08-31) various changes should be made to the `BaseCommand` base class implementation in `chain/`'s `base_command.py`:

- The `__init__` method should first check the arglist and set a local `help_mode` variable if the `--help` flag is present.

- The `main` method should be changed by introducing some conditional control logic, as follows:
  - In the first step, _unless_ `help_mode`, call  `do_initializations` (otherwise do nothing)
  - In the third step, the unconditional call to `do_command` should instead branch on the value of `help_mode`:
    - if True: call the new method `do_help`
    - if False: call `do_command`

- A new `do_help` method, with funtionality as described below, should be added:
  - First print out the command's detailed `help_text`
  - Next print a listing of its subcommands, if any.  Each subcommand in the list is presented on one line with the following contents, in left-aligned columns:
    - the canonical name of the subcommand
      - possibly suffixed with an ellipsis `…` when the command has sub-subcommands
    - the subcommand's short `description` text

- The `do_command` method should be changed to simply call `do_help`.

Once these changes are in place we can go through and update the existing command implementations as needed.
