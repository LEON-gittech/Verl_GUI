<template>
  <head>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.1/css/all.min.css">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
  </head>
  <div class="main">
    <div class="section header">
      <div class="title">
        <img class="mat-icon" src="/icon.png" alt="BEPA icon">
        BEPA
      </div>
      <div class="subtitle">
        From Off-Policy to On-Policy: Enhancing GUI Agents via Bi-level
        <span class="gradient-text">Expert-to-Policy Assimilation</span>
      </div>

      <div class="author-list">
        <span class="author">Zezhou Wang<span class="ind">1</span></span>
        <span class="author">Ziyun Zhang<span class="ind">2</span></span>
        <span class="author">Xiaoyi Zhang<span class="ind">3</span></span>
        <span class="author">Zhuzhong Qian<span class="ind">1</span></span>
        <span class="author">Yan Lu<span class="ind">3</span></span>
      </div>
      <div class="author-list">
        <span class="org"><span class="ind">1</span>Nanjing University</span>
        <span class="org"><span class="ind">2</span>Peking University</span>
        <span class="org"><span class="ind">3</span>Microsoft Research Asia</span>
      </div>

      <span class="link-block">
        <a href="#" class="external-link button is-normal is-rounded is-dark" @click.prevent="openLink('paper')">
          <span class="icon"><i class="fas fa-file-pdf"></i></span>
          <span>Paper</span>
        </a>
      </span>
      <span class="link-block">
        <a href="https://github.com/LEON-gittech/Verl_GUI" target="_blank" class="external-link button is-normal is-rounded is-dark">
          <span class="icon"><i class="fab fa-github"></i></span>
          <span>Code</span>
        </a>
      </span>
      <span class="link-block">
        <a href="https://huggingface.co/LEONW24/BEPA-7B-S2" target="_blank" class="external-link button is-normal is-rounded is-dark">
          <span class="icon"><img src="/hf-logo.png" class="hf-icon" /></span>
          <span>Model</span>
        </a>
      </span>
      <span class="link-block">
        <a href="https://huggingface.co/LEONW24/BEPA-7B-S2" target="_blank" class="external-link button is-normal is-rounded is-dark">
          <span class="icon"><i class="fa fa-database"></i></span>
          <span>Data</span>
        </a>
      </span>
    </div>

    <div class="abstract-section">
      <div class="abstract-title">Abstract</div>
      <p class="abstract-text">
        Vision-language models are increasingly deployed as <b>computer-use agents (CUAs)</b> that operate desktops and browsers. Top-performing CUAs are framework-based systems that decompose planning and execution, while <b>end-to-end screenshot-to-action policies</b> are easier to deploy but lag behind on benchmarks. We ask: <i>how can reinforcement learning from verifiable rewards (RLVR) best exploit a small pool of expert trajectories to train end-to-end policies?</i> Naïvely mixing these off-policy traces into on-policy RLVR is brittle: expert trajectories exhibit <b>structural mismatch</b> and <b>distribution shift</b> from the learner. We propose <b>BEPA</b> (Bi-Level Expert-to-Policy Assimilation), which turns static expert traces into policy-aligned guidance via self-rolled reachable trajectories (<b>LEVEL-1</b>) and a per-task, dynamically updated cache used in RLVR (<b>LEVEL-2</b>). On OSWorld-Verified, BEPA improves UITARS1.5-7B success from <span class="highlight-num">22.87%</span> to <span class="highlight-num">32.13%</span> and raises a held-out split from <span class="highlight-num">5.74%</span> to <span class="highlight-num">10.30%</span>.
      </p>
    </div>

    <div class="section">
      <div class="section-title">The Challenge: Expert-to-Policy Mismatch</div>
      <div class="challenge-grid">
        <div class="challenge-card">
          <div class="challenge-icon">⚡</div>
          <div class="challenge-header">Structural Mismatch</div>
          <p>Framework-based expert traces interleave multiple roles (planner, executor, grounder) and operate in tool-level action spaces that end-to-end policies cannot directly imitate.</p>
        </div>
        <div class="challenge-card">
          <div class="challenge-icon">📊</div>
          <div class="challenge-header">Distribution Gap</div>
          <p>Even after format conversion, expert trajectories lie far from the base-policy manifold, causing exploration collapse or unstable optimization in RLVR.</p>
        </div>
      </div>
      <el-card class="teaser" style="margin-top: 24px;">
        <el-image src="./stats/distribution_bias.png"></el-image>
      </el-card>
      <p class="figure-caption"><b>Expert-to-policy distribution mismatch in GUI agents.</b> Raw expert traces (Agent S2) are structurally incompatible with end-to-end models. Even after conversion, the token-probability distribution remains biased toward low-probability regions.</p>
    </div>

    <div class="section">
      <div class="section-title">BEPA: Bi-Level Expert-to-Policy Assimilation</div>
      <el-card class="teaser overview-card">
        <el-image src="./stats/overview.png"></el-image>
      </el-card>
      <p class="figure-caption"><b>Figure:</b> BEPA overview. LEVEL-1 initializes a policy-compatible guidance seed by re-rolling expert plans under the base policy. LEVEL-2 maintains a self-aligned per-task cache using the agent's own emerging successes, keeping the off-policy guidance aligned with the evolving on-policy manifold.</p>
      <p class="intro" style="margin-top: 20px;">BEPA operates in two complementary stages:</p>
      <div class="method-cards">
        <el-card class="method-card">
          <div class="method-header">LEVEL-1: Self-Rolled Execution</div>
          <p>Transforms alien expert traces into policy-compatible trajectories. We abstract the expert trajectory into a compact natural-language plan, then let the base policy act in the environment with plan conditioning. This produces trajectories that lie much closer to the policy's manifold.</p>
        </el-card>
        <el-card class="method-card">
          <div class="method-header">LEVEL-2: Self-Aligned Assimilation</div>
          <p>Dynamically maintains a per-task cache, injecting guided trajectories into GRPO updates only upon <i>total on-policy failure</i>. The cache is continuously refreshed with the policy's own successful executions, ensuring the off-policy signal evolves alongside the agent.</p>
        </el-card>
      </div>
    </div>

    <div class="section">
      <div class="section-title">Why Naive Integration Fails</div>
      <div class="challenge-grid three-col">
        <div class="challenge-card">
          <div class="challenge-icon">🔧</div>
          <div class="challenge-header">Structural Incompatibility</div>
          <p>Framework-based experts interleave multiple roles (planner, executor, grounder) and use tool-level actions that end-to-end policies cannot directly imitate.</p>
        </div>
        <div class="challenge-card">
          <div class="challenge-icon">📉</div>
          <div class="challenge-header">Distribution Shift</div>
          <p>Even after format conversion, expert trajectories lie far from the base-policy manifold, causing exploration collapse or unstable optimization.</p>
        </div>
        <div class="challenge-card">
          <div class="challenge-icon">🔄</div>
          <div class="challenge-header">Environmental Dynamics</div>
          <p>OSWorld tasks have transient UI elements (popups, banners, time-sensitive states) that static SFT replay cannot adapt to.</p>
        </div>
      </div>
      <el-card class="teaser" style="margin-top: 24px;">
        <el-image src="./stats/dynamic_env.png"></el-image>
      </el-card>
      <p class="figure-caption"><b>Dynamic environment challenges.</b> The initial state and valid actions can change across episodes due to human-verification popups, time-sensitive banners, and other transient UI elements. SFT encourages the policy to replay expert action sequences under an idealized history rather than adapting to variations.</p>
    </div>

    <div class="section">
      <div class="section-title">Main Results on OSWorld-Verified</div>
      <div class="stats-highlight">
        <div class="stat-card">
          <div class="stat-value">32.13%</div>
          <div class="stat-label">OSWorld Success Rate</div>
        </div>
        <div class="stat-card">
          <div class="stat-value green">+9.26</div>
          <div class="stat-label">Points vs Baseline</div>
        </div>
        <div class="stat-card">
          <div class="stat-value">10.30%</div>
          <div class="stat-label">Held-out Generalization</div>
        </div>
      </div>
      <el-card class="teaser">
        <el-table :data="resultsData" stripe style="width: 100%">
          <el-table-column prop="method" label="Method" width="280">
            <template #default="scope">
              <span :class="{ 'highlight-row': scope.row.highlight, 'category-row': scope.row.isCategory }">{{ scope.row.method }}</span>
            </template>
          </el-table-column>
          <el-table-column prop="expert_only" label="D_expert_only" align="center" />
          <el-table-column prop="train" label="D_train" align="center" />
          <el-table-column prop="held_out" label="D_held_out" align="center" />
          <el-table-column prop="overall" label="Overall (%)" align="center">
            <template #default="scope">
              <span :class="{ 'highlight-row': scope.row.highlight }">{{ scope.row.overall }}</span>
            </template>
          </el-table-column>
        </el-table>
      </el-card>
    </div>

    <div class="section">
      <div class="section-title">Why BEPA Works</div>

      <div class="subsection-title">Token Probability Alignment</div>
      <p class="intro">BEPA progressively aligns expert guidance with the policy's intrinsic manifold through bi-level assimilation. LEVEL-1 makes expert solutions reachable by re-rolling under the base policy; LEVEL-2 keeps guidance synchronized with the evolving policy through dynamic cache updates.</p>
      <div class="metrics-inline">
        <span>Tail mass (p&lt;0.2): <span class="metric-red">58.33%</span> → <span class="metric-green">10.53%</span></span>
        <span>JS divergence: <span class="metric-red">0.1525</span> → <span class="metric-green">0.0366</span></span>
      </div>
      <el-card class="teaser" style="max-width: 900px; margin: 16px auto;">
        <el-image src="./stats/histogram.png"></el-image>
      </el-card>
      <p class="figure-caption">Token-probability distributions: Raw Agent S2 traces (gray) → converted (green) → LEVEL-1 self-rolled (orange) → LEVEL-2 cache-updated (purple). The histograms show progressive reduction in low-probability tail mass.</p>

      <div class="subsection-title">Training Dynamics</div>
      <p class="intro">BEPA maintains stable training dynamics throughout the learning process, avoiding the entropy collapse seen in SFT-based approaches while steadily improving both on-policy and off-policy success likelihood.</p>
      <div class="dynamics-grid">
        <div class="dynamics-item">
          <el-card class="dynamics-card">
            <el-image src="./stats/entropy.png" fit="contain"></el-image>
          </el-card>
          <p class="dynamics-caption"><b>(a) Policy Entropy:</b> SFT+RL collapses during the SFT stage; LEVEL-1 shows entropy drop due to plan-conditioned guidance; BEPA sustains a moderate entropy profile.</p>
        </div>
        <div class="dynamics-item">
          <el-card class="dynamics-card">
            <el-image src="./stats/offpolicy_logprob.png" fit="contain"></el-image>
          </el-card>
          <p class="dynamics-caption"><b>(b) Off-policy Avg. Log-prob:</b> BEPA improves off-policy success likelihood steadily, indicating gradual assimilation.</p>
        </div>
        <div class="dynamics-item">
          <el-card class="dynamics-card">
            <el-image src="./stats/onpolicy_logprob.png" fit="contain"></el-image>
          </el-card>
          <p class="dynamics-caption"><b>(c) On-policy Avg. Log-prob:</b> BEPA improves on-policy successes without degrading learning.</p>
        </div>
      </div>

      <div class="subsection-title">Self-Aligned Cache Updates</div>
      <p class="intro">The cache updating frequency rises in tandem with policy performance, demonstrating that rising competence drives active cache updates and sustains effective assimilation of expert guidance into the policy.</p>
      <el-card class="teaser" style="max-width: 700px; margin: 16px auto;">
        <el-image src="./stats/success_rate_and_updating_frequency.png"></el-image>
      </el-card>
      <p class="figure-caption">The evolution of expert-only success (D<sub>expert_only</sub>) versus cache updating frequency. As policy performance improves, the cache is updated more frequently with the agent's own successful trajectories.</p>

      <div class="subsection-title">Ablation Summary</div>
      <el-card class="teaser" style="max-width: 600px; margin: 16px auto;">
        <div class="ablation-row">
          <span class="ablation-method">Trace Replacement</span>
          <div class="ablation-bar-wrap"><div class="ablation-bar" style="width: 74%;"></div></div>
          <span class="ablation-val">23.91%</span>
        </div>
        <div class="ablation-row">
          <span class="ablation-method">LEVEL-1 only</span>
          <div class="ablation-bar-wrap"><div class="ablation-bar" style="width: 85%;"></div></div>
          <span class="ablation-val">27.30%</span>
        </div>
        <div class="ablation-row">
          <span class="ablation-method">LEVEL-2 only</span>
          <div class="ablation-bar-wrap"><div class="ablation-bar" style="width: 93%;"></div></div>
          <span class="ablation-val">29.74%</span>
        </div>
        <div class="ablation-row highlight">
          <span class="ablation-method">BEPA (Full)</span>
          <div class="ablation-bar-wrap"><div class="ablation-bar full" style="width: 100%;"></div></div>
          <span class="ablation-val highlight">32.13%</span>
        </div>
      </el-card>
    </div>

    <div class="section">
      <div class="section-title">Case Studies</div>
      <div class="case-grid">
        <el-card class="case-card" @click="emergenceDialog = true">
          <el-image src="./stats/case_study/emergence/bepa_1.png" fit="cover" class="case-img"></el-image>
          <div class="case-info">
            <div class="case-title">Case 1: Capability Emergence</div>
            <p>Opening VLC media player in Ubuntu OS - a capability that emerges through bi-level assimilation.</p>
            <span class="case-link">Click to view details</span>
          </div>
        </el-card>
        <el-card class="case-card" @click="distributionDialog = true">
          <el-image src="./stats/case_study/distribution/bepa_1.png" fit="cover" class="case-img"></el-image>
          <div class="case-info">
            <div class="case-title">Case 2: Distribution Alignment</div>
            <p>How BEPA aligns expert trajectories with the policy's native action distribution.</p>
            <span class="case-link">Click to view details</span>
          </div>
        </el-card>
      </div>
    </div>

    <el-dialog v-model="emergenceDialog" title="Case Study: Capability Emergence" width="90%" top="5vh">
      <div class="dialog-content">
        <h4>UITARS (Fails to find VLC)</h4>
        <div class="dialog-images">
          <el-image
            v-for="i in 5"
            :key="i"
            :src="'./stats/case_study/emergence/uitars_' + i + '.png'"
            :preview-src-list="emergenceUitarsImages"
            :initial-index="i - 1"
            fit="contain"
            class="dialog-img"
          />
        </div>
        <h4>Agent S2 (Expert)</h4>
        <div class="dialog-images">
          <el-image
            src="./stats/case_study/emergence/s2_1.png"
            :preview-src-list="['./stats/case_study/emergence/s2_1.png']"
            fit="contain"
            class="dialog-img"
          />
        </div>
        <h4>BEPA (Success)</h4>
        <div class="dialog-images">
          <el-image
            src="./stats/case_study/emergence/bepa_1.png"
            :preview-src-list="['./stats/case_study/emergence/bepa_1.png']"
            fit="contain"
            class="dialog-img"
          />
        </div>
      </div>
    </el-dialog>

    <el-dialog v-model="distributionDialog" title="Case Study: Distribution Alignment" width="90%" top="5vh">
      <div class="dialog-content">
        <h4>Agent S2 (Expert - High distribution bias)</h4>
        <div class="dialog-images">
          <el-image
            v-for="i in 2"
            :key="i"
            :src="'./stats/case_study/distribution/s2_' + i + '.png'"
            :preview-src-list="distributionS2Images"
            :initial-index="i - 1"
            fit="contain"
            class="dialog-img"
          />
        </div>
        <h4>Converted (Format aligned but still biased)</h4>
        <div class="dialog-images">
          <el-image
            v-for="i in 2"
            :key="i"
            :src="'./stats/case_study/distribution/converted_' + i + '.png'"
            :preview-src-list="distributionConvertedImages"
            :initial-index="i - 1"
            fit="contain"
            class="dialog-img"
          />
        </div>
        <h4>BEPA (Policy-aligned)</h4>
        <div class="dialog-images">
          <el-image
            v-for="i in 3"
            :key="i"
            :src="'./stats/case_study/distribution/bepa_' + i + '.png'"
            :preview-src-list="distributionBepaImages"
            :initial-index="i - 1"
            fit="contain"
            class="dialog-img"
          />
        </div>
      </div>
    </el-dialog>

    <div class="section">
      <div class="section-title">Verl-GUI: Training Framework</div>
      <el-card class="teaser">
        <el-image src="./stats/verl_gui_architecture.png"></el-image>
      </el-card>
      <p class="intro">
        <b>GUI agent training framework comparison.</b> Left: veRL uses a single colocated cluster where all workers share resources and communicate synchronously. Right: Verl-GUI separates training and rollout into independent Ray clusters, enabling heterogeneous hardware allocation and async task queue for decoupled consumption.
      </p>
      <div class="section-title" style="font-size: 1.4em; margin-top: 32px;">Key Features</div>
      <div class="features-grid">
        <div class="feature-item">
          <div class="feature-icon"><i class="fas fa-server"></i></div>
          <div class="feature-content">
            <div class="feature-name">Heterogeneous Cluster Architecture</div>
            <p>Separates trainer and rollout into independent Ray clusters. IB/NVLink nodes for training, PCIe nodes for rollout.</p>
          </div>
        </div>
        <div class="feature-item">
          <div class="feature-icon"><i class="fas fa-cloud"></i></div>
          <div class="feature-content">
            <div class="feature-name">Multiple Storage Backends</div>
            <p>Supports Azure Blob Storage, NAS, and local filesystems through a unified abstraction layer.</p>
          </div>
        </div>
        <div class="feature-item">
          <div class="feature-icon"><i class="fas fa-tasks"></i></div>
          <div class="feature-content">
            <div class="feature-name">Async Task Queue</div>
            <p>Dynamically maintains a task queue for decoupled and non-blocking task processing.</p>
          </div>
        </div>
        <div class="feature-item">
          <div class="feature-icon"><i class="fas fa-sync"></i></div>
          <div class="feature-content">
            <div class="feature-name">K-round Rollout Processing</div>
            <p>Intelligently splits batches across multiple rounds for flexible scaling.</p>
          </div>
        </div>
        <div class="feature-item">
          <div class="feature-icon"><i class="fas fa-expand-arrows-alt"></i></div>
          <div class="feature-content">
            <div class="feature-name">Scalable Parallel Environments</div>
            <p>Concurrent environments scale with compute capacity, with Ray orchestration and auto Docker cleanup.</p>
          </div>
        </div>
        <div class="feature-item">
          <div class="feature-icon"><i class="fas fa-cubes"></i></div>
          <div class="feature-content">
            <div class="feature-name">Service-oriented Orchestration</div>
            <p>Modular components including CheckpointManager, EnvWorkerPool, RolloutService.</p>
          </div>
        </div>
      </div>
    </div>

    <section class="section" id="BibTeX" style="text-align: left;">
      <div class="container is-max-desktop content" style="max-width: 100%; margin: 0 auto;">
        <div class="bibtex-header">
          <h3 class="section-title" style="font-size: 1.4em; margin: 0;">BibTeX</h3>
          <button @click="copyBibtex" class="copy-button" :class="{ 'copied': copySuccess }">
            <i class="fas" :class="copySuccess ? 'fa-check' : 'fa-copy'"></i>
            {{ copySuccess ? 'Copied!' : 'Copy' }}
          </button>
        </div>
        <div class="bibtex-container">
          <pre><code>@article{wang2025bepa,
  title = {From Off-Policy to On-Policy: Enhancing GUI Agents via Bi-level Expert-to-Policy Assimilation},
  author = {Wang, Zezhou and Zhang, Ziyun and Zhang, Xiaoyi and Qian, Zhuzhong and Lu, Yan},
  journal = {arXiv preprint},
  year = {2025}
}</code></pre>
        </div>
      </div>
    </section>

    <div class="footer">
      This website is inspired by <el-link href="https://mathvista.github.io/">MathVista</el-link> and <el-link href="https://nerfies.github.io/">Nerfies</el-link>.
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { ElMessage } from 'element-plus'

const emergenceDialog = ref(false)
const distributionDialog = ref(false)
const copySuccess = ref(false)

// Image arrays for preview
const emergenceUitarsImages = [
  './stats/case_study/emergence/uitars_1.png',
  './stats/case_study/emergence/uitars_2.png',
  './stats/case_study/emergence/uitars_3.png',
  './stats/case_study/emergence/uitars_4.png',
  './stats/case_study/emergence/uitars_5.png'
]
const distributionS2Images = [
  './stats/case_study/distribution/s2_1.png',
  './stats/case_study/distribution/s2_2.png'
]
const distributionConvertedImages = [
  './stats/case_study/distribution/converted_1.png',
  './stats/case_study/distribution/converted_2.png'
]
const distributionBepaImages = [
  './stats/case_study/distribution/bepa_1.png',
  './stats/case_study/distribution/bepa_2.png',
  './stats/case_study/distribution/bepa_3.png'
]

const resultsData = ref([
  { method: 'Proprietary & Framework-based Models', isCategory: true, expert_only: '', train: '', held_out: '', overall: '' },
  { method: 'Agent S2', expert_only: '-', train: '-', held_out: '-', overall: '33.00' },
  { method: 'Jedi-7B w/ o3', expert_only: '-', train: '-', held_out: '-', overall: '42.40' },
  { method: 'Doubao-1.5-Think', expert_only: '-', train: '-', held_out: '-', overall: '31.90' },
  { method: 'Claude-4-Sonnet', expert_only: '-', train: '-', held_out: '-', overall: '31.20' },
  { method: 'Open-Source End-to-End Models', isCategory: true, expert_only: '', train: '', held_out: '', overall: '' },
  { method: 'UITARS-72B-DPO', expert_only: '-', train: '-', held_out: '-', overall: '24.00' },
  { method: 'GUI-Owl-7B', expert_only: '-', train: '-', held_out: '-', overall: '32.10' },
  { method: 'OpenCUA-7B', expert_only: '-', train: '-', held_out: '-', overall: '24.30' },
  { method: 'UITARS1.5-7B', expert_only: '18.52', train: '55.12', held_out: '5.74', overall: '22.87' },
  { method: 'Training Methods (UITARS1.5-7B+)', isCategory: true, expert_only: '', train: '', held_out: '', overall: '' },
  { method: 'SFT', expert_only: '5.56', train: '47.77', held_out: '1.65', overall: '17.65' },
  { method: 'GRPO', expert_only: '11.11', train: '58.02', held_out: '5.32', overall: '23.60' },
  { method: 'Trace Replacement', expert_only: '18.52', train: '66.50', held_out: '1.29', overall: '23.91' },
  { method: 'LUFFY', expert_only: '19.01', train: '65.44', held_out: '2.16', overall: '24.11' },
  { method: 'Ablations', isCategory: true, expert_only: '', train: '', held_out: '', overall: '' },
  { method: 'LEVEL-1', expert_only: '25.93', train: '69.20', held_out: '5.05', overall: '27.30' },
  { method: 'LEVEL-2', expert_only: '29.18', train: '71.65', held_out: '7.48', overall: '29.74' },
  { method: 'BEPA (ours)', highlight: true, expert_only: '35.19', train: '73.23', held_out: '10.30', overall: '32.13' },
])

const openLink = (type) => {
  ElMessage.info('Coming soon!')
}

const copyBibtex = async () => {
  const bibtex = `@article{wang2025bepa,
  title = {From Off-Policy to On-Policy: Enhancing GUI Agents via Bi-level Expert-to-Policy Assimilation},
  author = {Wang, Zezhou and Zhang, Ziyun and Zhang, Xiaoyi and Qian, Zhuzhong and Lu, Yan},
  journal = {arXiv preprint},
  year = {2025}
}`
  try {
    await navigator.clipboard.writeText(bibtex)
    copySuccess.value = true
    setTimeout(() => { copySuccess.value = false }, 2000)
  } catch (err) {
    console.error('Failed to copy:', err)
  }
}
</script>

<style scoped>
.main {
  text-align: center;
  color: #23272f;
  font-family: 'Inter', 'Helvetica Neue', Arial, sans-serif;
  max-width: 1300px;
  margin: 0 auto;
  padding: 0 24px;
  background: #fff;
}

.header {
  margin: 48px 0 32px 0 !important;
}

.title {
  font-size: 4.5em;
  font-weight: 900;
  background: linear-gradient(90deg, #ff3d3d 0%, #ff6b6b 25%, #3b82f6 75%, #1d4ed8 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  letter-spacing: -2px;
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 18px;
}

.subtitle {
  font-size: 2.4em;
  color: #1f2937;
  font-weight: 700;
  line-height: 1.25;
  max-width: 1100px;
  margin: 0 auto 16px auto;
  letter-spacing: -0.01em;
}

.gradient-text {
  background: linear-gradient(90deg, #ff3d3d 0%, #ff6b6b 30%, #3b82f6 70%, #1d4ed8 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.author-list {
  margin: 20px 0 0 0;
  line-height: 1.8;
  font-size: 1.25em;
  color: #1f2937;
  font-weight: 500;
}

.author {
  margin: 0 8px;
  display: inline-block;
}

.author a {
  color: hsl(204, 86%, 53%);
  text-decoration: none;
}

.author a:hover {
  text-decoration: underline;
}

.org {
  margin: 0 10px;
  color: #374151;
  font-size: 1.1rem;
  font-weight: 500;
}

.ind {
  font-size: 0.7em;
  vertical-align: super;
  color: hsl(204, 86%, 53%);
}

.section {
  margin: 40px 0;
  padding: 0;
}

/* Abstract Section */
.abstract-section {
  text-align: left;
  max-width: 1100px;
  margin: 36px auto;
  padding: 28px 32px;
  background: linear-gradient(135deg, #f8fafc 0%, #f1f5f9 100%);
  border-radius: 12px;
  border: 1px solid #e2e8f0;
}

.abstract-title {
  font-size: 1.4em;
  font-weight: 800;
  color: #1f2937;
  margin-bottom: 16px;
  letter-spacing: -0.01em;
}

.abstract-text {
  font-size: 1.1em;
  line-height: 1.75;
  color: #374151;
  margin: 0;
}

.abstract-text b {
  color: #1f2937;
}

.highlight-num {
  font-weight: 700;
  color: #dc2626;
}

/* Figure Caption */
.figure-caption {
  text-align: center;
  font-size: 0.95em;
  color: #4b5563;
  max-width: 900px;
  margin: 12px auto 0 auto;
  line-height: 1.6;
}

/* Challenge Cards */
.challenge-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 24px;
  margin-top: 20px;
}

.challenge-grid.three-col {
  grid-template-columns: repeat(3, 1fr);
}

/* Subsection Titles */
.subsection-title {
  font-size: 1.3em;
  font-weight: 700;
  color: #1f2937;
  margin: 32px 0 16px;
  text-align: left;
  padding-left: 14px;
  border-left: 4px solid #3b82f6;
}

.challenge-card {
  background: #fff;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  padding: 24px;
  text-align: left;
  transition: box-shadow 0.2s, transform 0.2s;
}

.challenge-card:hover {
  box-shadow: 0 4px 12px rgba(0,0,0,0.08);
  transform: translateY(-2px);
}

.challenge-icon {
  font-size: 2rem;
  margin-bottom: 12px;
}

.challenge-header {
  font-size: 1.15em;
  font-weight: 700;
  color: #1f2937;
  margin-bottom: 10px;
}

.challenge-card p {
  font-size: 1em;
  line-height: 1.65;
  color: #4b5563;
  margin: 0;
}

.overview-card {
  max-width: 1000px;
}

.section-title {
  margin: 36px 0 20px;
  font-size: 1.85em;
  font-weight: 800;
  color: #1f2937;
  text-align: left;
  letter-spacing: -0.01em;
}

/* Side by side layout for image-text sections */
.side-by-side {
  display: flex;
  align-items: flex-start;
  gap: 2rem;
  margin: 1.5rem 0;
}

.side-by-side .side-image {
  flex: 1;
  max-width: 50%;
}

.side-by-side .side-image :deep(.el-card) {
  margin: 0;
}

.side-by-side .side-text {
  flex: 1;
  text-align: justify;
}

.side-by-side .side-text p {
  margin: 0;
  font-size: 1.08em;
  line-height: 1.6;
  color: #23272f;
}

.mat-icon {
  width: 1.2em;
  height: 1.2em;
  margin-right: 0.1em;
  vertical-align: middle;
  display: inline-block;
}

.hf-icon {
  width: 1.1em;
  height: 1.1em;
  vertical-align: middle;
}

.teaser {
  max-width: 100%;
  margin: 20px auto;
  border-radius: 10px;
  background: #fff;
  box-shadow: none;
  border: 1px solid #dbdbdb;
  overflow: hidden;
}

.intro {
  text-align: justify;
  font-size: 1.08em;
  max-width: 1100px;
  line-height: 1.6;
  margin: 0 auto 12px auto;
  color: #23272f;
}

.intro-list {
  text-align: left;
  max-width: 1100px;
  margin: 0 auto 16px auto;
  font-size: 1.08em;
  line-height: 1.6;
  color: #23272f;
}

.intro-list li {
  margin-bottom: 8px;
}

.link-block {
  margin: 16px 0;
}

.external-link {
  display: inline-flex;
  align-items: center;
  padding: 8px 16px;
  margin: 4px;
  border: none;
  border-radius: 290486px;
  background: #363636;
  color: #fff;
  text-decoration: none;
  font-size: 1rem;
  font-weight: 400;
  transition: background 0.2s;
}

.external-link:hover {
  background: #4a4a4a;
}

.external-link .icon {
  margin-right: 8px;
  color: #fff;
}

/* Method Cards */
.method-cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(420px, 1fr));
  gap: 24px;
  max-width: 100%;
  margin: 0 auto;
}

.method-card {
  text-align: left;
  padding: 24px;
  border-radius: 12px;
  border: 2px solid #e5e7eb;
  transition: border-color 0.2s, box-shadow 0.2s;
}

.method-card:hover {
  border-color: #3b82f6;
  box-shadow: 0 4px 16px rgba(59, 130, 246, 0.1);
}

.method-header {
  font-size: 1.2rem;
  font-weight: 700;
  color: #1f2937;
  margin-bottom: 12px;
  font-family: 'Google Sans', sans-serif;
  display: flex;
  align-items: center;
  gap: 8px;
}

.method-header::before {
  content: '';
  width: 4px;
  height: 24px;
  background: linear-gradient(180deg, #3b82f6, #8b5cf6);
  border-radius: 2px;
}

.method-card p {
  font-size: 1.05rem;
  line-height: 1.7;
  color: #4b5563;
}

.metric-row {
  margin-top: 14px;
  padding-top: 10px;
  border-top: 1px solid #dbdbdb;
  text-align: center;
  font-size: 0.9rem;
  color: #4a4a4a;
}

.metric-red { color: #f14668; font-weight: 600; }
.metric-green { color: #48c774; font-weight: 600; }

.metrics-inline {
  display: flex;
  justify-content: center;
  gap: 32px;
  margin: 12px 0 16px;
  font-size: 1rem;
  color: #4b5563;
}

/* Training Dynamics */
.dynamics-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
  max-width: 100%;
  margin: 0 auto;
}

.dynamics-item {
  text-align: center;
}

.dynamics-card {
  padding: 8px;
}

.dynamics-caption {
  font-size: 0.875rem;
  color: #4a4a4a;
  line-height: 1.5;
  margin-top: 8px;
  text-align: center;
}

/* Stats */
.stats-highlight {
  display: flex;
  justify-content: center;
  gap: 28px;
  margin-bottom: 28px;
  flex-wrap: wrap;
}

.stat-card {
  text-align: center;
  padding: 24px 36px;
  background: linear-gradient(135deg, #f8fafc 0%, #f1f5f9 100%);
  border-radius: 16px;
  border: 1px solid #e2e8f0;
  min-width: 180px;
}

.stat-value {
  font-size: 2.5rem;
  font-weight: 800;
  color: #1f2937;
  font-family: 'Google Sans', sans-serif;
  letter-spacing: -0.02em;
}

.stat-value.green {
  color: #059669;
}

.stat-label {
  font-size: 0.95rem;
  color: #6b7280;
  margin-top: 8px;
  font-weight: 500;
}

.highlight-row {
  font-weight: 600;
  color: #363636;
}

.category-row {
  font-weight: 600;
  color: #363636;
  font-size: 0.9rem;
}

/* Ablation */
.ablation-title {
  font-size: 1.1rem;
  font-weight: 700;
  color: #1f2937;
  margin-bottom: 16px;
  text-align: left;
  font-family: 'Google Sans', sans-serif;
}

.ablation-row {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 10px;
}

.ablation-method {
  width: 140px;
  text-align: left;
  font-size: 0.9rem;
  color: #4b5563;
  font-weight: 500;
}

.ablation-bar-wrap {
  flex: 1;
  height: 22px;
  background: #f1f5f9;
  border-radius: 6px;
  overflow: hidden;
}

.ablation-bar {
  height: 100%;
  background: linear-gradient(90deg, #93c5fd, #60a5fa);
  border-radius: 6px;
}

.ablation-bar.full {
  background: linear-gradient(90deg, #3b82f6, #8b5cf6);
}

.ablation-val {
  width: 70px;
  text-align: right;
  font-size: 0.95rem;
  color: #4b5563;
  font-weight: 600;
}

.ablation-row.highlight .ablation-method,
.ablation-val.highlight {
  font-weight: 700;
  color: #1f2937;
}

/* Case Studies */
.case-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
  gap: 24px;
  max-width: 100%;
  margin: 0 auto;
}

.case-card {
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
  overflow: hidden;
  border-radius: 12px;
  border: 1px solid #e2e8f0;
}

.case-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.1);
}

.case-img {
  height: 200px;
  width: 100%;
}

.case-info {
  padding: 18px;
  text-align: left;
}

.case-title {
  font-size: 1.05rem;
  font-weight: 700;
  color: #1f2937;
  margin-bottom: 10px;
  font-family: 'Google Sans', sans-serif;
}

.case-info p {
  font-size: 0.95rem;
  color: #4b5563;
  line-height: 1.55;
  margin-bottom: 12px;
}

.case-link {
  font-size: 0.9rem;
  color: #3b82f6;
  font-weight: 500;
}

/* Dialog */
.dialog-content h4 {
  font-size: 1.05rem;
  color: #1f2937;
  margin: 20px 0 12px;
  padding-bottom: 8px;
  border-bottom: 2px solid #1f2937;
  font-family: 'Google Sans', sans-serif;
  font-weight: 700;
}

.dialog-images {
  display: flex;
  gap: 14px;
  flex-wrap: wrap;
}

.dialog-img {
  max-width: 280px;
  border-radius: 8px;
  border: 1px solid #e2e8f0;
  transition: transform 0.2s;
}

.dialog-img:hover {
  transform: scale(1.02);
}

/* Features */
.features-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
  gap: 18px;
  max-width: 100%;
  margin: 0 auto;
}

.feature-item {
  display: flex;
  gap: 14px;
  padding: 18px;
  background: linear-gradient(135deg, #f8fafc 0%, #f1f5f9 100%);
  border-radius: 10px;
  border: 1px solid #e2e8f0;
  text-align: left;
  transition: box-shadow 0.2s, transform 0.2s;
}

.feature-item:hover {
  box-shadow: 0 4px 12px rgba(0,0,0,0.06);
  transform: translateY(-2px);
}

.feature-icon {
  font-size: 1.35rem;
  color: #3b82f6;
  flex-shrink: 0;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #eff6ff;
  border-radius: 8px;
}

.feature-name {
  font-size: 1rem;
  font-weight: 700;
  color: #1f2937;
  margin-bottom: 6px;
  font-family: 'Google Sans', sans-serif;
}

.feature-content p {
  font-size: 0.9rem;
  color: #4b5563;
  line-height: 1.55;
  margin: 0;
}

/* BibTeX */
.bibtex-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}

.copy-button {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 8px 14px;
  background: #f8fafc;
  border: 1px solid #e2e8f0;
  border-radius: 6px;
  color: #1f2937;
  font-size: 0.875rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
}

.copy-button:hover {
  background: #1f2937;
  color: #fff;
  border-color: #1f2937;
}

.copy-button.copied {
  background: #059669;
  color: #fff;
  border-color: #059669;
}

.bibtex-container {
  background: #f8fafc;
  padding: 1.25rem;
  border-radius: 10px;
  text-align: left;
  white-space: pre;
  overflow-x: auto;
  border: 1px solid #e2e8f0;
  margin: 16px 0;
}

pre {
  margin: 0;
  font-family: 'SF Mono', 'Monaco', 'Consolas', monospace;
}

code {
  font-family: 'SF Mono', 'Monaco', 'Consolas', monospace;
  color: #1f2937;
  font-size: 0.875rem;
  line-height: 1.6;
}

.footer {
  color: #6b7280;
  margin: 56px 0 32px;
  padding-top: 24px;
  border-top: 1px solid #e5e7eb;
  font-size: 0.9rem;
  text-align: center;
}

@media (max-width: 768px) {
  .main {
    max-width: 100vw;
    padding: 0 16px;
  }
  .title {
    font-size: 1.75rem;
    flex-direction: column;
    gap: 8px;
  }
  .subtitle {
    font-size: 1rem;
  }
  .author-list {
    font-size: 1rem;
  }
  .dynamics-grid {
    grid-template-columns: 1fr;
  }
  .stats-highlight {
    flex-direction: column;
    align-items: center;
  }
  .challenge-grid,
  .case-grid, .method-cards, .features-grid {
    grid-template-columns: 1fr;
  }
  .side-by-side {
    flex-direction: column;
  }
  .side-by-side .side-image {
    max-width: 100%;
  }
  .abstract-section {
    padding: 20px;
  }
  .subsection-title {
    font-size: 1.1em;
    margin: 24px 0 12px;
  }
  .stat-card {
    min-width: 140px;
    padding: 18px 24px;
  }
  .stat-value {
    font-size: 2rem;
  }
}
</style>
