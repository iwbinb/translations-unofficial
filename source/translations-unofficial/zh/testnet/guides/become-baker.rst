
.. _networkDashboardLink: https://dashboard.testnet.concordium.com/
.. _node-dashboard: http://localhost:8099
.. _Discord: https://discord.com/invite/xWmQ5tp

.. _become-a-baker:

==================================
成为面包师（创建块）
==================================

.. contents::
   :local:
   :backlinks: none

本节说明面包师是什么，面包师在网络中的角色以及如何成为面包师。

通过阅读本节，您将学到：

-  什么是面包师及其相关概念.
-  如何升级您的节点成为面包师.

成为面包师的过程可以概括为以下步骤：

＃ 获取一个帐户和一些GTU。

＃ 获取一组面包师密钥。

＃ 将面包师密钥注册到该帐户。

＃ 使用面包师密钥启动节点。

完成这些步骤后，面包师节点将烘培块。 如果是烘培块被添加到链中时，节点的面包师将获得奖励。

.. note::

   在本节中，我们将使用名称  ``bakerAccount``  作为用来注册和管理面包师的帐户。

定义
===========

面包师密钥
-----

当节点通过创建并添加到链中的新区块来积极参与网络时，它就是 *面包师* （或 *正在烘培* ）。 
面包师收集、订购、验证包含在区块中的交易，以维护区块链的完整性。 
面包师对每个烘培的块进行签名，以便网络的其他参与者可以检查和执行该块。

面包师
----------

每个面包师都有一组称为 *面包师密钥* 。节点使用这些密钥来签名它烘培的块。 
特定的面包师为了烘培一块并签名，他的节点必须在加载其面包师密钥集的情况下运行。

面包师账户 
-------------

每个账户可以用一组面包师密钥来注册面包师。

每当面包师烘培一个有效的块后，在一段时间后奖励将支付给关联账户。


质押和抽奖
-----------------

.. todo::

   - 链接到发布时间表.

该账户可以将质押一部分GTU余额到 *面包师质押* ，以后可以手动释放全部或部分抵押额。 
在面包师释放之前，质押的金额不能移动或转移。

.. note::

   如果某个账户拥有已发布的计划转帐的金额，则即使金额尚未到达，也可以质押

为了得到烘培一个块，面包师必须参加 *抽奖* ，其中获得中奖彩票的几率与质押金额成正比。

同样的股份在计算面包师是否包含在最终委员会中时使用，使用相。 请参见 Finalization_。

.. _epochs-and-slots:

Epochs和slots
----------------

在Concordium区块链中，时间细分为 *slots* 。slots的持续时间固定在创世块中。
在任何给定的分支上，每个slots最多可以有一个块，但是可以在同一slots中产生不同分支上的多个块。

.. todo::

   让我们添加一张图片

在考虑奖励和其他与烘焙有关的概念时，我们使用 *epoch* 的概念作为时间单位，它定义了固定当前面包师和质押的时间段。 
Epochs的时长固定在创世块中。 
在测试网中，Epochs的持续时间为 **1小时** 。

开始烘培
============

管理账户
-----------------

本节简要介绍了导入账户的相关步骤。 有关完整的说明，请参见 :ref:`managing_accounts`。

使用 :ref:`concordium_id`应用程序创建账户。成功创建帐户后，导航至  **More**  标签并选择  **Export** 允许您获取包含账户信息的JSON文件。
允许您获取包含户信息的JSON文件。

将账户导入到toolchain中运行

.. code-block:: console

   $concordium-client config account import <path/to/exported/file> --name bakerAccount

``concordium-client`` 将要求输入密码以解密导出的文件并导入所有账户。
相同的密码也将用于加密交易签名密钥和加密的转移密钥。

创建一个面包师密钥并且注册成为面包师
--------------------------------------------

.. note::

   账户需要拥有一些GTU，因此请务必在APP中的账户请求获取100GTU。
   

每个账户都有一个唯一的面包师ID，该ID在注册其面包师时使用。
该ID必须由网络提供，并且当前无法预先计算。
必须在面包师密钥文件中将此ID赋予节点，以便它可以使用面包师密钥创建块。 
执行以下操作时， ``concordium-client``  将自动填写此字段。

创建一组新的密钥，运行：

.. code-block:: console

   $concordium-client baker generate-keys <keys-file>.json

您可以在其中为密钥文件选择一个任意名称。 
要在网络中注册密钥，您需要运行节点 :ref:`running a node <running-a-node>` 并向网络发送 ``baker add`` 交易：

.. code-block:: console

   $concordium-client baker add <keys-file>.json --sender bakerAccount --stake <amountToStake> --out <concordium-data-dir>/baker-credentials.json

替换

- ``<amountToStake>`` 面包师初始质押金额
- ``<concordium-data-dir>`` 用以下目录:

  * on Linux and MacOS: ``~/.local/share/concordium``
  * on Windows: ``%LOCALAPPDATA%\\concordium``.

(输出文件名字保留 ``baker-credentials.json``).

提供一个  ``--no-restake`` 标志，以避免自动将奖励添加到面包师的质押金额上。 
在  `Restaking the earnings`_. 部分中对此行为进行了说明。

为了使用这些面包师密钥启动节点并开始生成块，您首先需要关闭当前正在运行的节点（通过在节点正在运行的终端上按 ``Ctrl + C``  或使用 ``concordium-node-stop``  可执行文件）。

将文件放置在适当的目录中之后（指定输出文件时已在上一个命令中完成），然后使用 ``concordium-node`` 再次启动该节点。 
当面包师包含在当前epoch中时，该节点将自动开始烘焙。

此更改将立即执行，并且在其中一个epoch之后结束时生效。
添加面包师的事务包含在一个块中。

.. table:: 时间轴：添加面包师

   +-------------------------------------------+-----------------------------------------+-----------------+
   |                                           | 当交易已在区块中                           |  2 epochs 之后   |
   +===========================================+=========================================+=================+
   | 通过查询节点可以看到更改                      |  ✓                                      |                 |
   +-------------------------------------------+-----------------------------------------+-----------------+
   | 在面包师委员会里的面包师                      |                                         | ✓               |
   +-------------------------------------------+-----------------------------------------+-----------------+

.. note::

   如果在epoch `E` 期间将添加面包师的事务在一个区块中，则在epoch `E+2` 开始时，面包师将被视为烘焙委员会的一部分。

管理面包师
==================

检查面包师状态和他的彩票能力
------------------------------------------------------

为了查看节点是否正在烘焙，您可以检查各种来源，
在显示的信息中提供不同程度的精度。

- 在 `网络仪表盘 <http://dashboard.testnet.concordium.com>`_,中，
  节点将在 ``Baker`` 列中显示其面包师ID。
- 使用  ``concordium-client`` ，您可以检查当前面包师的列表以及他们持有的相对质押金额，即他们的抽奖能力。
  抽奖能力将决定给定的面包师赢得彩票和烘培大奖的可能性
   
  
  .. code-block:: console

     $concordium-client consensus show-parameters --include-bakers
     Election nonce:      07fe0e6c73d1fff4ec8ea910ffd42eb58d5a8ecd58d9f871d8f7c71e60faf0b0
     Election difficulty: 4.0e-2
     Bakers:
                                  Account                       Lottery power
             ----------------------------------------------------------------
         ...
         34: 4p2n8QQn5akq3XqAAJt2a5CsnGhDvUon6HExd2szrfkZCTD4FX   <0.0001
         ...

- 使用  ``concordium-client`` ，您可以检查账户是否具有注册一个面包师以及该面包师所质押的当前金额。 

  .. code-block:: console

     $./concordium-client account show bakerAccount
     ...

     Baker: #22
      - Staked amount: 10.000000 GTU
      - Restake earnings: yes
     ...

- 如果质押量足够大，并且有一个节点与面包师一起运行
  加载密钥后，该面包师最终应该会产生块，您可以看到
  在您的手机钱包中，该账户已收到烘烤奖励，
  如下图所示：

  .. image:: images/bab-reward.png
     :align: center
     :width: 250px

更新的质押金额
--------------------------

更新质押金额，请运行：

.. code-block:: console

   $concordium-client baker update-stake --stake <newAmount> --sender bakerAccount

修改质押金额将修改面包师烘培块的概率。

当面包师 **首次添加质押或增加质押** 时，更改将在链上执行，在一个区块交易立即可见中（可以通过 ``concordium-client account show bakerAccount`` ）
并在此2个epochs之后生效。

.. table:: 时间线: 增加质押

   +----------------------------------------+-----------------------------------------+----------------+
   |                                        | 当交易已在区块中                           | 2 epochs 之后  |
   +========================================+=========================================+================+
   | 通过查询节点可以看到更改                   | ✓                                       |                |
   +----------------------------------------+-----------------------------------------+----------------+
   | 面包师使用新质押                          |                                         | ✓              |
   +----------------------------------------+-----------------------------------------+----------------+

当面包师 **减少质押金额** 时，更改将需要 *2 +bakerCooldownEpochs* epochs生效。
更改将在链上执行，在一个区块交易立即可见中（可以通过 ``concordium-client account show bakerAccount``  ）

.. code-block:: console

   $concordium-client account show bakerAccount
   ...

   Baker: #22
    - Staked amount: 50.000000 GTU to be updated to 20.000000 GTU at epoch 261  (2020-12-24 12:56:26 UTC)
    - Restake earnings: yes

   ...

.. table:: 时间线: 减少质押

   +----------------------------------------+-----------------------------------------+----------------------------------------+
   |                                        | 当交易已在区块中                           | 2 epochs 之后                          |
   +========================================+=========================================+========================================+
   | 通过查询节点可以看到更改                   | ✓                                       |                                        |
   +----------------------------------------+-----------------------------------------+----------------------------------------+
   | 面包师使用新质押                          |                                         | ✓                                      |
   +----------------------------------------+-----------------------------------------+----------------------------------------+
   | 再次减少质押 或者                         | ✗                                       | ✓                                      |
   | 注销包面师                               |                                         |                                        |
   +----------------------------------------+-----------------------------------------+----------------------------------------+

.. note::

   在测试网中， ``bakerCooldownEpochs``  最初设置为168个epochs。 这个值可以用如下方式查看：

   .. code-block:: console

      $concordium-client raw GetBlockSummary
      ...
              "bakerCooldownEpochs": 168
      ...

.. warning::

   如  `Definitions`_ 部分所述，质押金额被 *locked* ，即无法转让或用于付款。 
   你应该考虑到这点并存入一个短期不需要转移的金额。特别是要注销面包师或修改质押时，您需要有一些未质押的GTU来支付交易的费用。

复利(再次质押所得)
----------------------

当以面包师身份参加网络和烘焙块时，该账户在每个烤块上获得奖励。 
这些奖励会默认自动添加到为质押金额。

您可以选择修改此行为，变成接收奖励到账户余额，而不会自动投注。 
此开关可以通过 ``concordium-client`` 更改：

.. code-block:: console

   $concordium-client baker update-restake False --sender bakerAccount
   $concordium-client baker update-restake True --sender bakerAccount

对restake标志的更改将立即生效； 但是，变化将在下一个epoch开始影响烘培并最终确定功率。 
目前可以在帐户信息中查询到开关值，通过使用  ``concordium-client``

.. code-block:: console

   $concordium-client account show bakerAccount
   ...

   Baker: #22
    - Staked amount: 50.000000 GTU
    - Restake earnings: yes

   ...

.. table:: 时间线: 复利(再次质押所得)

   +-----------------------------------------------+-----------------------------------------+-------------------------------+
   |                                               | 当交易已在区块中                           | 获利完 2 epochs 之后           |
   +===============================================+=========================================+===============================+
   | 通过查询节点可以看到更改                          | ✓                                       |                               |
   +-----------------------------------------------+-----------------------------------------+-------------------------------+
   | 奖励将会自动estaked                             | ✓                                       |                               |
   +-----------------------------------------------+-----------------------------------------+-------------------------------+
   | 如果自动restaking，获利后的的本金会影响彩票能力     |                                         | ✓                             |
   +-----------------------------------------------+-----------------------------------------+-------------------------------+

重新注册面包师后，它将自动重新获得收益，但是如上所述，可以通过向面包师添加命令通过  ``--no-restake``  标志来更改此收益，如下所示：

.. code-block:: console

   $concordium-client baker add baker-keys.json --sender bakerAccount --stake <amountToStake> --out baker-credentials.json --no-restake

终定
------------

最终确定是指 *最终确定委员会* 中的节点执行投票过程，当委员会中有足够多的成员接收该区块并就其结果达成共识时，该 *最终确定* 该区块。 
较新的块必须具有终定块作为先驱，以确保链的完整性。
有关此过程的更多信息，请参见章节 :ref:`finalization<glossary-finalization>` 

终定委员会由拥有一定质押的面包师们组成。 
这特别意味着，要参加终定委员会，您可能必须修改质押金额才能达到上述阈值。 
在测试网中，参与终定委员会所需的质押金额是 **现有GTU总额的0.1％** 。

参与终定委员会会在终定的每个区块上产生奖励。 
奖励将在区块完成后的某个时间支付给面包师账户。

移除面包师
================
控制账户可以选择在链上注销其面包师。 为此，您必须执行 ``concordium-client`` ：

.. code-block:: console

   $concordium-client baker remove --sender bakerAccount

这会将面包师从面包师列表中删除，并解锁面包师上的质押金额，以便可以自由转移或移动它。

移除面包师时，更改的时间与减少质押的时间相同。 更改将需要 * 2 + bakerCooldownEpochs * 个epochs才能生效。 
一旦将交易包含在一个区块中，该更改就会在链上可见。
您可以照常使用 ``concordium-client`` 查询账户信息来检查此更改何时生效：

.. code-block:: console

   $concordium-client account show bakerAccount
   ...

   Baker #22 to be removed at epoch 275 (2020-12-24 13:56:26 UTC)
    - Staked amount: 20.000000 GTU
    - Restake earnings: yes

   ...

.. table:: 时间线: 注销面包师

   +--------------------------------------------+-----------------------------------------+----------------------------------------+
   |                                            | 当交易已在区块中                           |  *2 + bakerCooldownEpochs* epochs 之后 |
   +============================================+=========================================+========================================+
   | 通过查询节点可以看到更改                       | ✓                                       |                                        |
   +--------------------------------------------+-----------------------------------------+----------------------------------------+
   | 面包师从面包师委员会中移除                     |                                         | ✓                                      |
   +--------------------------------------------+-----------------------------------------+----------------------------------------+

.. warning::

   减少质押金额和移除面包师不能同时进行。 
   在通过减少质押金额而产生的冷却期间，无法移除面包师，反之亦然。

支持与反馈
==================

如果您遇到任何问题或建议，请在 `Discord`_ 上发布您的问题或反馈，或通过testnet@concordium.com与我们联系。
