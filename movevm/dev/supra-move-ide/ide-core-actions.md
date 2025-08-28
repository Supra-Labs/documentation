# IDE Core Actions

## Actions: Compile, Deploy, & Test

These action buttons in the Explorer tab allow you to interact with your Move code and the Supra blockchain.

<figure><img src="https://netalabs.gitbook.io/supra-ide/~gitbook/image?url=https%3A%2F%2Flh7-rt.googleusercontent.com%2Fdocsz%2FAD_4nXf-KpxKMEFc7NEgcHojYeE8CYKfEMr2vItgVt0zrcORPefeXId-SO1SLsfY3l-wGTncnJyU1EGHVOOFGrkDewBBeJp42EsSV7CZg2GD2GAucctwYp_ibLTnP_0s7V2-LGe6g47tdA%3Fkey%3D4NMBPIGagqp7hkgc5QATsw&#x26;width=300&#x26;dpr=4&#x26;quality=100&#x26;sign=f49c7e49&#x26;sv=2" alt="" width="375"><figcaption></figcaption></figure>

* **Compile**: Click the _Compile_ button to convert your Move source code into bytecode. The progress and any success messages or errors will be displayed in the Console at the bottom.
* **Deploy**: Click the _Deploy_ button to publish your compiled project to the Supra blockchain. This action requires a connected wallet to sign the transaction. A confirmation with a link to the transaction on SupraScan will appear in the Console.
* **Run Tests**: Click the _Test_ button to run any tests you've written for your project. A popup will allow you to filter which tests to run, or you can leave it blank to run all of them. The test results will stream into the Console.

## Success and error showcases

### Compile successfully

<figure><img src="https://netalabs.gitbook.io/supra-ide/~gitbook/image?url=https%3A%2F%2Flh7-rt.googleusercontent.com%2Fdocsz%2FAD_4nXf33c05A7-jpVFoOt-I9mTrRiS8kqBaoGQTZlK0-5K8VvikKZbqknv0HqfK8znV_oGIZjfN1THc2kpRx6jYdQkhNVTgEsb7aSaXxhDCsYOmoVZjVzILh8_CYj8u5wJwalaJUX0ayA%3Fkey%3D4NMBPIGagqp7hkgc5QATsw&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=182a9af9&#x26;sv=2" alt=""><figcaption></figcaption></figure>

### Compile failed

<figure><img src="https://netalabs.gitbook.io/supra-ide/~gitbook/image?url=https%3A%2F%2Flh7-rt.googleusercontent.com%2Fdocsz%2FAD_4nXcO_NGbC1Goo0TxWoea9X0NyvFAiJKE20btQA9hLbEP9U_y41WwM-pQxIS5RoqgmmAkajSW57HDCTAMNyxR2eyKdhto0Wh7VNMq4gC3PklI5r4pAwH_Zsh3cO8Mx3ec4Egrx0thow%3Fkey%3D4NMBPIGagqp7hkgc5QATsw&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=41bcca59&#x26;sv=2" alt=""><figcaption></figcaption></figure>

### Test successfully

<figure><img src="https://netalabs.gitbook.io/supra-ide/~gitbook/image?url=https%3A%2F%2Flh7-rt.googleusercontent.com%2Fdocsz%2FAD_4nXcB-_E7HbzocMHah2FasvoHUx90G1bui1Y15NIOs-fcBJrT4LBwzO4X9WefvixUnlJUlGifYopqzhjrrQ8wgbgOpeWvc19h_l28xncerQ6nrzsSNFexYX3GDFLR04R9rsmuDYoZ%3Fkey%3D4NMBPIGagqp7hkgc5QATsw&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=22c1bb54&#x26;sv=2" alt=""><figcaption></figcaption></figure>

### Test failed

<figure><img src="https://netalabs.gitbook.io/supra-ide/~gitbook/image?url=https%3A%2F%2Flh7-rt.googleusercontent.com%2Fdocsz%2FAD_4nXc0c_EgbXXg6MfxRgJkwZittZsr0QvurCLL6tRT6GVFrjGHh3A6QFCTL4YqP-sjP0W9Xm7WLbk0wNxUgSM5NqHxh6mbVWf95fYv0FlKQo15xkaO1HPsMVNnREBErOZGKy1XYHuxiw%3Fkey%3D4NMBPIGagqp7hkgc5QATsw&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=8fa7081b&#x26;sv=2" alt=""><figcaption></figcaption></figure>

### Build successfully but failed to deploy contract

<figure><img src="https://netalabs.gitbook.io/supra-ide/~gitbook/image?url=https%3A%2F%2Flh7-rt.googleusercontent.com%2Fdocsz%2FAD_4nXcI8gB7mbyWVu91Ykyu3TAAQWJL_7olVDUL9HHVUVzNxh-096mc0dGF6nxxpwkZbTtjcwl-2-tqxhsVRQBH9F5DxOpMWU4lmnlKy9BjkE_eETkTGAtkJzskxNmHGPB64NLhXNd4Fg%3Fkey%3D4NMBPIGagqp7hkgc5QATsw&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=16781a09&#x26;sv=2" alt=""><figcaption></figcaption></figure>

### Deploy contract successfully

<figure><img src="https://netalabs.gitbook.io/supra-ide/~gitbook/image?url=https%3A%2F%2Flh7-rt.googleusercontent.com%2Fdocsz%2FAD_4nXe1p_1S5zK3oYq9abUjHBhNt0HokkGHlfg9yDK9ISUredU3jygD3ThO6xtyhcR4962fJW5CXSdT0rAbdfCPMAZ33xT0mJ0eDjaI3jcV9yk2k0RIfJu6RJtf5c3cI9pTppAxllko4Q%3Fkey%3D4NMBPIGagqp7hkgc5QATsw&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=7cf118dc&#x26;sv=2" alt=""><figcaption></figcaption></figure>
