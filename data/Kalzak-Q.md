## Deterministic address deployment only uses the 160 least significant bits in `deployCounterFactualWallet`

In the function [SmartAccountFactory.deployCounterFactualWallet](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L33), the user provides an argument `_index` that can be used to change the salt for the `create2` deployment. This argument is originally type `uint256` but casted to `uint160` and then to `address`. This casting results in the 96 most significant bits in `_index` not being used to determine `salt`. 

This can cause confusion, as the user would rightly believe that by changing `_index` they are guaranteed to have a different deployment salt, which is not the case. 

Consider either:

1. Changing `_index` to be `uint160` so there are no values to truncate.
2. Changing the salt calculation so that `_index` is not casted to a smaller type.

## Test
A test demonstrating this behavior is shown below:
```diff
diff --git a/entrypoint.test.ts.orig b/entrypoint.test.ts
index 29e930c..b811d5e 100644
--- a/entrypoint.test.ts.orig
+++ b/entrypoint.test.ts
@@ -1424,3 +1424,59 @@ describe("EntryPoint", function () {
     });
   });
 });
+
+describe("Deployment", function () {
+  it.only("Deterministic salt truncated", async () => {
+
+    let baseImpl;
+    let walletFactory;
+    let owner;
+    let accounts;
+    let user;
+    let other;
+
+    // Get accounts
+    accounts = await ethers.getSigners();
+    owner = await accounts[0];
+    user = await accounts[1];
+    other = await accounts[2];
+
+    // Deploy smart account implementation
+    const BaseImplementation = await ethers.getContractFactory("SmartAccount");
+    baseImpl = await BaseImplementation.deploy();
+    await baseImpl.deployed();
+
+    const WalletFactory = await ethers.getContractFactory("SmartAccountFactory");
+    walletFactory = await WalletFactory.deploy(baseImpl.address);
+    await walletFactory.deployed();
+
+    /*
+      The least significant 160 bits are same between salt1 and salt2
+      The least significant 160 bits are different between salt1 and salt3
+
+      salt1 = 0x00ffffffffffffffffffffffffffffffffffffffff
+      salt2 = 0x99ffffffffffffffffffffffffffffffffffffffff
+      salt3 = 0x00ffffffffffffffffffffffffffffffffffffff00
+    */
+
+    let salt1 = BigNumber.from("1461501637330902918203684832716283019655932542975");
+    let salt2 = BigNumber.from("225071252148959049403367464238307585027013611618303");
+    let salt3 = BigNumber.from("1461501637330902918203684832716283019655932542720");
+
+    // First deploy works fine
+    await walletFactory.connect(user).deployCounterFactualWallet(user.address, other.address, other.address, salt1);
+
+    // salt1 and salt2 are different values (difference is not within the 160 least significant bits)
+    expect(salt1).to.not.equal(salt2);
+
+    // But deployment will still fail
+    await expect(walletFactory.connect(user).deployCounterFactualWallet(user.address, other.address, other.address, salt2))
+      .to.be.revertedWith("Create2 call failed");
+
+    // salt1 and salt3 are different values (difference is within the 160 least significant bits)
+    expect(salt1).to.not.equal(salt3);
+
+    // This deployment will work
+    walletFactory.connect(user).deployCounterFactualWallet(user.address, other.address, other.address, salt3);
+  });
+});
\ No newline at end of file
```