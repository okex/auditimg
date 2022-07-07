# Solana NFT Market 合约审计

**审计时间：06.07~06.22**
## 1、审计资料
- 【文档】：https://okg-block.larksuite.com/wiki/wikusfmAndWFO7J5DVOl7kq3TWd
- 【合约代码】：https://github.com/okex/solana-nft/tree/efd6fa14846d69eb8c58241e472939ead448c124

## 2、审计范围
| 目录  | 文件  | SHA-256哈希值  |
|:----------|:----------|:----------|
| program/src | constants.rs | 0d016eff693cd0f0dd2cbc0e1a7a459022a449f17036055bae0040016318c23c  |
| program/src | entrypoint.rs | 6e26acdb519adb9c52c45188744d06531991fd649c61d674d2d225961bdabbbd  |
| program/src | error.rs | a8b8a79b3e56fa92b7cb8ae761dd29dd41785e00cc91d70b46ba621f22f15303  |
| program/src | instruction.rs | f3836e579280e1bc3e8fecf499ddb47e31d62ea27cea9a8250bf4c20f12922e1  |
| program/src | lib.rs | 53ab7423eca6d2ee18ee66f5a3fa6bd0cfa3fe8e2aec1934cce45d2b7b1be6b8  |
| program/src | pda.rs | ef6591601d64f461d1aa632fa4981825f2b549e490db7422128620017fc3c24b  |
| program/src | processor.rs | 8761346fcc6be0ec219c21df03ec2a1503d00e3f9dfea555a1d742687bcf35c6  |
| program/src | state.rs | ec215b87b535d47318c8975c1ab09765fcab72e7adc35cdd821c9e5870130347  |
| program/src | utils.rs | 9d425b9739f4ccdebb8115c448622b3db844d5622e2fd1a8c1523dd5636871c3 |

## 2、审计目的 

本次审计的目的是为了审阅自研Solana NFT Market项目基于Rust语言编写的NFT交易市场功能，研究其设计、架构，发现潜在的安全隐患，并试图找到可能存在的漏洞。

## 3、审计方式

通过清晰地理解该项目的设计目的、运行原理和实现方式，审计团队对合约代码进行了深入的研究和分析。在厘清各个合约及其函数的调用关系的基础上，对合约可能存在的漏洞进行了定位及分析。最终产生问题描述和给出相应的修改意见。
审计方法

| Static analysis  | Manual Review  |
|:----------|:----------|


## 4、项目概述

Solana NFT Market主要实现了NFT市场买卖单成交，允许卖家进行卖单挂单或买家进行买单挂单。主动挂单订单数据会上链存储，对手方的进行吃单成交。
项目还实现了取消订单、更新订单价格以及进行费率设置等功能。

## 5、文件描述

```Shell
program/src
├── constants.rs #变量定义
├── entrypoint.rs #指令入口
├── error.rs #业务逻辑错误定义
├── instruction.rs #指令定义
├── lib.rs #模块定义
├── pda.rs #业务中相关PDA账号
├── processor.rs #指令处理代码
├── state.rs #状态定义
└── utils.rs #辅助函数
```

## 6、审计结果

| name  | level  | status  |
|:----------|:----------|:----------|
| 重入    | 无    |     |
| 注入    | 无    |    |
| 权限绕过    | 无    |    |
| mempool抢跑   | 无    |     |
| 回滚    | 无    |     |
| 条件竞争    | 无    |     |
| 循环耗尽gas    | 无    |     |
| 闪电贷的高影响    | 无    |     |
| 经济模型不合理    | 无    |     |
| 投票权管理混乱    | 无    |     |
| 可预见的随机数    | 无    |     |
| 数据隐私泄露    | 无    |     |
| 链上时间使用不当    | 无    |     |
| fallback函数编码不当    | 无    |     |
| 鉴权不当    | 无   |    |
| 内置函数使用不当    | 无    |    |
| 内联汇编使用不当    | 无    |    |
| 构造函数不规范    | 无    |    |
| 返回值不规范    | 无    |    |
| event不规范    | 无    |    |
| 关键字使用不规范    | 无   |     |
| 未遵循ERC标准    | 无    |    |
| 条件判断不规范    | 无    |     |
| 流动性枯竭风险    | 无   |     |
| 中心化风险    | 无    |    |
| 逻辑变更风险    | 无    |     |
| `整数溢出`    | `中`    |     |
| 函数可见性不当    | 无    |     |
| 变量初始化不当    | 无    |     |
| 合约间调用不当    | 无    |    |
| 变量不规范    | 无    |    |
| 重放    | 无   |     |
| 随机存储位置写入    | 无    |    |
| 蜜罐逻辑    | 无    |     |
| 哈希碰撞    | 无    |    |
| 领奖逻辑不当    | 无    |    |
| 使用不推荐的方法    | 无    |     |
| `未遵循基本编码原则`    | `低`    |    |
| 账户缺少签名者检查    | 无    |     |
| 账户缺少所有者检查   | 无    |     |
| 解析账户数据前未验证账户    | 无    |     |
| 缺少相同账户校验   | 无    |     |
| 账户无法安全关闭    | 无    |     |
| 账户数据类型混淆    | 无    |     |
| 缺少账户可写检查         | 无    |        |
| 过时的外部依赖         | 无    |     |

## 7、审计详情

**溢出风险 processor.rs Line722**

使用+ - * / **存在整数溢出风险。推荐不再使用+ - * / ** ，而使用checked_add checked_sub checked_mul and checked_div checked_pow等函数, 这些检查计算在Solana的成本几乎可以忽略不计。还可以在release mode打开overflow-checks，通过设置[profile.release]下的overflow-checks = true打开release模式下的溢出检查。

```Rust
    invoke(
        &system_instruction::transfer(
            maker_main_account.key,
            maker_wrapped_sol_account.key,
            //@OKLink Audit Description: 整数溢出风险
            //@OKLink Audit Solution: 使用checked_add
            new_price + Rent::get()?.minimum_balance(TokenAccount::LEN),
        ),
        &[
            maker_main_account.clone(),
            maker_wrapped_sol_account.clone(),
        ],
    )?;
```

**未遵循基本编码原则 processor.rs Line439 Line457**

代码进行条件判断满足业务逻辑需求，两处检查使用了相同的检查条件，给出了不同的报错返回。应修改第二处检查判断条件，或删除第二处检查。

```Rust
    if &order_info.maker_main_pubkey != maker_main_account.key {
        return Err(OkxNFTMarketError::OrderMakerNotMatch.into());
    }
    //@OKLink Audit Description: 两处检查判断条件相同
    //@OKLink Audit Solution: 修改或删除第二处检查
    // This must match so that maker can receive funds from taker correctly.
    if &order_info.maker_main_pubkey != maker_main_account.key {
        return Err(OkxNFTMarketError::MainAccountNotMatch.into());
    }

```

**数值计算精度 processor.rs Line500**

计算版税过程使用checked_div会导致结果存在精度丢失问题。平台业务逻辑进行数学运算进行token收取和发放过程，建议使用策略为发放token使用向下取整，收取token使用向上取整。另外，精度丢失问题也会产生攻击机会。

```Rust
    // caclculate royalty fee
    let royalty_fee = total_payments
        .checked_mul(seller_fee_basis_points as u64)
        .ok_or(OkxNFTMarketError::MathOverflow)?
        //@OKLink Audit Description: checked_div向下取整丢失精度
        //@OKLink Audit Solution: 使用checked_ceil_div向上取整
        .checked_div(10000)
        .ok_or(OkxNFTMarketError::MathOverflow)?;
```

**未遵循基本编码原则 processor.rs Line681**

价格更新处理函数process_update_price中，对new_price与原价格相同分支没有进行单独处理，目前代码逻辑会按照new_price低于原价格进行处理。

```Rust
    //@OKLink Audit Description: 未对价格不变进行单独处理
    //@OKLink Audit Solution: 如果价格不变可以直接返回
    if order_info.is_buy {
        if new_price > order_info.price {
            invoke(
                &system_instruction::transfer(
                    maker_main_account.key,
                    maker_wrapped_sol_account.key,
                    new_price - order_info.price,
                ),
                &[
                    maker_main_account.clone(),
                    maker_wrapped_sol_account.clone(),
                ],
            )?;

            invoke(
                &spl_token::instruction::sync_native(
                    &spl_token::id(),
                    maker_wrapped_sol_account.key,
                )?,
                &[maker_wrapped_sol_account.clone()],
            )?;
        } else {
            invoke_signed(
                &spl_token::instruction::close_account(
                    &spl_token::id(),
                    &maker_wrapped_sol_pubkey,
                    maker_main_account.key,
                    &maker_wrapped_sol_pubkey,
                    &[&maker_wrapped_sol_pubkey],
                )?,
                &[
                    maker_main_account.clone(),
                    maker_wrapped_sol_account.clone(),
                ],
                &[&[
                    WSOL_VAULT.as_bytes(),
                    maker_main_account.key.as_ref(),
                    order_account.key.as_ref(),
                    &[maker_bump],
                ]],
            )?;
            ...
        }
```

**未遵循基本编码原则 processor.rs Line910**

更新平台配置函数update_protocol_config_account中所传入的 status，protocol_fee_base_points，protocol_fee_recipient，protocol_manager等变量没有进行范围检查，可能导致设置错误数值。

```Rust
    fn update_protocol_config_account(
        account_info_iter: &mut Iter<AccountInfo>,
        status: Option<bool>,
        protocol_fee_base_points: Option<u16>,
        protocol_fee_recipient: Option<&Pubkey>,
        protocol_manager: Option<&Pubkey>,
    ) -> ProgramResult {
        let protocol_config_account = next_account_info(account_info_iter)?;
        let protocol_manager_account = next_account_info(account_info_iter)?;

        //@OKLink Audit Description: 未对传入参数进行范围检查
        //@OKLink Audit Solution: 增加对传入参数的范围检查

        if !protocol_manager_account.is_signer {
            return Err(ProgramError::MissingRequiredSignature);
        }

        if protocol_config_account.key != &find_protocol_config_address().0 {
            return Err(OkxNFTMarketError::ProtoclConfigAddressNotMatch.into());
        }
        ...
    }
```
