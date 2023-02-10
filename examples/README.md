# EXAMPLES OF GENERATOR FILES

## Scenarios Available

- `hipster_shop.yaml`: the classic hipster shop demo

- `mini_ebank.yaml`: an eBank application
    - 17 services
    - inferred services simulating mainframe and external apis
        * identify inferred services with `transaction.external:"true"`
        * label inferred services with `transaction.region` for mainframe and `transaction.target` for external APIs
    - allow RED metrics
        * on the frontend services (`ebank_android`, `ebank_iOS`, `ebank_web`)
        * on the mainframe external services
        * small error rates are generated for more realistic values
        * dashboards available in `./examples/mini_ebank_assets/`
    - generate errors
        * every 30 minutes at 1.00, 1.30, ... and for 15 minutes, the IAM generates 100% errors for ebank_iOS
    - additional documentation available on `./docs/doc_mini_ebank.pptx`


## Extra Scenarios Available

In `./examples/mini_ebank_assets/`, you will find an extra scenario allowing you to simulate an eBank with no errors (`mini_ebank_no_errors.yaml`) and then add the selected errors on demand from another injector:

- use `mini_ebank_iam_errors.yaml` to generate ebank_iOS IAM errors
