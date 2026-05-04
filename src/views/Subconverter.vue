<template>
  <div class="subconverter-page">
    <div class="subconverter-glow subconverter-glow--one"></div>
    <div class="subconverter-glow subconverter-glow--two"></div>
    <el-row class="subconverter-layout" style="margin-top: 10px">
      <el-col>
        <el-card class="subconverter-card">
          <div slot="header" class="subconverter-hero">
            <div class="subconverter-hero__copy">
              <span class="subconverter-hero__eyebrow">SUB WEB / NEXT</span>
              <div class="subconverter-hero__topline">
                <h1 class="subconverter-hero__title">订阅转换</h1>
                <div class="subconverter-hero__stats">
                  <div class="subconverter-stat subconverter-stat--backend">
                    <span>后端版本</span>
                    <strong>{{ backendVersion || "等待检测" }}</strong>
                  </div>
                </div>
              </div>
              <p class="subconverter-hero__desc">
                在线订阅转换场景，适配 Clash、Surge、Sing-Box 等常见使用环境。
              </p>
            </div>
          </div>
          <el-container class="subconverter-container">
            <el-form class="subconverter-form" :model="form" label-width="92px" label-position="left" style="width: 100%">
              <el-form-item label="订阅链接:">
                <el-input v-model="form.sourceSubUrl" type="textarea" rows="3"
                  placeholder="支持各种订阅链接或单节点链接，多个链接每行一个或用 | 分隔" />
              </el-form-item>
              <el-form-item label="生成类型:">
                <el-select v-model="form.clientType" style="width: 100%">
                  <el-option v-for="(v, k) in options.clientTypes" :key="k" :label="k" :value="v"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="后端地址:">
                <el-select v-model="form.customBackend" allow-create filterable @change="selectChanged"
                  placeholder="可输入自己的后端" style="width: 100%">
                  <el-option v-for="(v, k) in options.customBackend" :key="k" :label="k" :value="v"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="短链选择:">
                <el-select v-model="form.shortType" allow-create filterable placeholder="可输入其他可用短链API"
                  style="width: 100%">
                  <el-option v-for="(v, k) in options.shortTypes" :key="k" :label="k" :value="v"></el-option>
                </el-select>
              </el-form-item>

              <el-form-item label="远程配置:">
                <el-select v-model="form.remoteConfig" allow-create filterable placeholder="请选择" style="width: 100%">
                  <el-option-group v-for="group in options.remoteConfig" :key="group.label" :label="group.label">
                    <el-option v-for="item in group.options" :key="item.value" :label="item.label"
                      :value="item.value"></el-option>
                  </el-option-group>
                </el-select>
              </el-form-item>
              <el-form-item class="subconverter-advanced__wrap" label-width="0px">
                <el-collapse class="subconverter-advanced">
                  <el-collapse-item>
                    <template slot="title">
                      <el-form-item label="高级功能:" style="width: 100%;">
                        <el-button type="limr" style="width: 100%;" icon="el-icon-more-outline">点击显示/隐藏
                        </el-button>
                      </el-form-item>
                    </template>
                    <el-form-item label="包含节点:">
                      <el-input v-model="form.includeRemarks" placeholder="要保留的节点，支持正则" />
                    </el-form-item>
                    <el-form-item label="排除节点:">
                      <el-input v-model="form.excludeRemarks" placeholder="要排除的节点，支持正则" />
                    </el-form-item>
                    <el-form-item label="节点命名:">
                      <el-input v-model="form.rename" placeholder="举例：`a@b``1@2`，|符可用\转义" />
                    </el-form-item>
                    <el-form-item label="远程设备:">
                      <el-input v-model="form.devid" placeholder="用于设置QuantumultX的远程设备ID" />
                    </el-form-item>
                    <el-form-item label="更新间隔:">
                      <el-input v-model="form.interval" placeholder="返用于设置托管配置更新间隔，单位为天" />
                    </el-form-item>
                    <el-form-item label="订阅命名:">
                      <el-input v-model="form.filename" placeholder="返回的订阅文件名，可以在支持文件名的客户端中显示出来" />
                    </el-form-item>
                    <el-form-item class="eldiy" label-width="0px">
                      <el-row type="flex">
                        <el-col>
                          <el-checkbox v-model="form.nodeList" label="仅输出节点信息" border></el-checkbox>
                        </el-col>
                        <el-popover placement="bottom" v-model="form.extraset">
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.emoji" label="Emoji"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.insert" label="插入默认节点"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.udp" label="启用 UDP"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.xudp" label="启用 XUDP"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.tfo" label="启用 TFO"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.sort" label="基础节点排序"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.tpl.clash.doh" label="Clash.DoH"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.appendType" label="插入节点类型"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.tpl.surge.doh" label="Surge.DoH"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.tls13" label="开启TLS_1.3"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.expand" label="展开规则全文"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.new_name" label="Clash新字段名"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.scv" label="跳过证书验证"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.fdn" label="过滤不支持节点"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <div style="margin-left: 35%">
                                <el-checkbox v-model="form.tpl.singbox.ipv6" label="Sing-Box支持IPV6"></el-checkbox>
                              </div>
                            </el-col>
                          </el-row>
                          <el-button slot="reference">更多选项</el-button>
                        </el-popover>
                      </el-row>
                    </el-form-item>
                  </el-collapse-item>
                </el-collapse>
              </el-form-item>
              <div style="margin-top: 30px"></div>
              <el-form-item class="subconverter-output" label="定制订阅:">
                <el-input class="copy-content" disabled v-model="customSubUrl">
                  <el-button slot="append" v-clipboard:copy="customSubUrl" v-clipboard:success="onCopy" ref="copy-btn"
                    icon="el-icon-document-copy">复制
                  </el-button>
                </el-input>
              </el-form-item>
              <el-form-item class="subconverter-output" label="订阅短链:">
                <el-input class="copy-content" v-model="customShortSubUrl" placeholder="输入自定义短链接后缀，点击生成短链接可反复生成">
                  <el-button slot="append" v-clipboard:copy="customShortSubUrl" v-clipboard:success="onCopy"
                    ref="copy-btn" icon="el-icon-document-copy">复制
                  </el-button>
                </el-input>
              </el-form-item>
              <el-form-item class="subconverter-action-row" label-width="0px" style="margin-top: 40px; text-align: center">
                <el-button class="subconverter-main-btn" style="width: 120px" type="danger" @click="makeUrl"
                  :disabled="form.sourceSubUrl.length === 0 || btnBoolean">生成订阅链接
                </el-button>
                <el-button class="subconverter-main-btn subconverter-main-btn--alt" style="width: 120px" type="danger" @click="makeShortUrl" :loading="loading1"
                  :disabled="customSubUrl.length === 0">生成短链接
                </el-button>
                <el-button class="subconverter-main-btn subconverter-main-btn--parse" style="width: 120px" type="primary"
                  icon="el-icon-copy-document" @click="dialogLoadConfigVisible = true" :loading="loading3">从URL解析
                </el-button>
              </el-form-item>
            </el-form>
          </el-container>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      backendVersion: "",
      customSubUrl: "",
      customShortSubUrl: "",
      loading1: false,
      loading3: false,
      btnBoolean: false,
      dialogLoadConfigVisible: false,
      loadConfig: "",
      form: {
        sourceSubUrl: "",
        clientType: "clash",
        // 【修改点】默认后端地址设置为你的地址
        customBackend: "https://api.rrys.eu.cc", 
        shortType: "https://v1.mk/short",
        remoteConfig: "https://raw.githubusercontent.com/cmliu/ACL4SSR/main/Clash/config/ACL4SSR_Online.ini",
        includeRemarks: "",
        excludeRemarks: "",
        rename: "",
        devid: "",
        interval: "",
        filename: "",
        nodeList: false,
        extraset: false,
        emoji: true,
        insert: false,
        udp: false,
        xudp: false,
        tfo: false,
        sort: false,
        appendType: false,
        tls13: false,
        expand: true,
        new_name: true,
        scv: false,
        fdn: false,
        tpl: {
          clash: { doh: false },
          surge: { doh: false },
          singbox: { ipv6: false }
        }
      },
      options: {
        clientTypes: {
          Clash: "clash",
          "Surge4/5": "surge&ver=4",
          "Sing-Box": "singbox",
          V2Ray: "v2ray",
          Trojan: "trojan",
          ShadowsocksR: "ssr",
          "混合订阅（mixed）": "mixed",
          Surfboard: "surfboard",
          Quantumult: "quan",
          "Quantumult X": "quanx",
          Loon: "loon",
          Mellow: "mellow",
          "自动判断客户端": "auto",
        },
        shortTypes: {
          "v1.mk": "https://v1.mk/short",
          "d1.mk": "https://d1.mk/short",
          "dlj.tf": "https://dlj.tf/short",
        },
        // 【修改点】后端列表首项修改为你的后端
        customBackend: {
          "我的私人后端": "https://api.rrys.eu.cc",
          "CM提供-负载均衡后端": "https://subapi.cmliussss.net",
          "CM提供-应急备用后端": "https://subapi.fxxk.dedyn.io",
          "肥羊提供-增强型后端": "https://url.v1.mk",
        },
        remoteConfig: [
          {
            label: "CM规则",
            options: [
              { label: "CM_Online 默认版", value: "https://raw.githubusercontent.com/cmliu/ACL4SSR/main/Clash/config/ACL4SSR_Online.ini" },
              { label: "CM_Online_MultiCountry 负载均衡", value: "https://raw.githubusercontent.com/cmliu/ACL4SSR/main/Clash/config/ACL4SSR_Online_MultiCountry.ini" }
            ]
          },
          {
            label: "ACL规则",
            options: [
              { label: "ACL_默认版", value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online.ini" },
              { label: "ACL_全分组版", value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Full.ini" }
            ]
          }
        ]
      }
    };
  },
  methods: {
    onCopy() {
      this.$message.success("已复制到剪贴板");
    },
    selectChanged(val) {
      this.form.customBackend = val;
    },
    makeUrl() {
      if (this.form.sourceSubUrl === "") {
        this.$message.error("订阅链接不能为空");
        return false;
      }
      // 这里简化了构建逻辑，实际项目中需拼接待所有参数
      let backend = this.form.customBackend + "/sub?";
      let params = `target=${this.form.clientType}&url=${encodeURIComponent(this.form.sourceSubUrl)}&insert=${this.form.insert}&config=${encodeURIComponent(this.form.remoteConfig)}&emoji=${this.form.emoji}&list=${this.form.nodeList}&udp=${this.form.udp}&tfo=${this.form.tfo}&scv=${this.form.scv}&fdn=${this.form.fdn}&sort=${this.form.sort}`;
      
      this.customSubUrl = backend + params;
      this.$message.success("定制订阅链接已生成");
    },
    makeShortUrl() {
        // 短链接生成逻辑...
    },
    confirmLoadConfig() {
        // 从 URL 解析逻辑...
    }
  }
};
</script>
