<script setup lang="ts">
import LocaleText from "@/components/text/localeText.vue";
import { fetchGet, fetchPost } from '@/utilities/fetch'
import {ref} from "vue";

import {
	Chart,
	LineElement,
	BarElement,
	BarController,
	LineController,
	LinearScale,
	Legend,
	Title,
	Tooltip,
	CategoryScale,
	PointElement,
	Filler
} from 'chart.js';
Chart.register(
	LineElement,
	BarElement,
	BarController,
	LineController,
	LinearScale,
	Legend,
	Title,
	Tooltip,
	CategoryScale,
	PointElement,
	Filler
);
import PeerSessions from "@/components/peerDetailsModalComponents/peerSessions.vue";
import PeerTraffics from "@/components/peerDetailsModalComponents/peerTraffics.vue";
import PeerEndpoints from "@/components/peerDetailsModalComponents/peerEndpoints.vue";
const props = defineProps(['selectedPeer'])
const selectedDate = ref(undefined)
const notifyEmails = ref('')
const notifyWebhooks = ref([])
const notifyEvents = ref([])
const availableWebhooks = ref([])
const notifyLoaded = ref(false)
const availableEvents = [
  { id: 'peer_went_online', label: 'Peer Went Online' },
  { id: 'peer_went_offline', label: 'Peer Went Offline' },
  { id: 'peer_endpoint_changed', label: 'Peer Endpoint Changed' }
]

function loadNotifyConfig() {
  if (!props.selectedPeer?.id) return
  notifyLoaded.value = false
  fetchGet(`/api/health/peer/${encodeURIComponent(props.selectedPeer.id)}/notify`, {}, (res) => {
    if (res.status) {
      notifyEmails.value = (res.data.emails || []).join(', ')
      notifyWebhooks.value = [...(res.data.webhooks || [])]
      notifyEvents.value = [...(res.data.events || ['peer_went_online', 'peer_went_offline', 'peer_endpoint_changed'])]
    }
    notifyLoaded.value = true
  })
  fetchGet('/api/webHooks/getWebHooks', {}, (res) => {
    availableWebhooks.value = res.data || []
  })
}

function saveNotifyConfig() {
  const emails = notifyEmails.value.split(',').map(e => e.trim()).filter(e => e.length > 0)
  fetchPost(`/api/health/peer/${encodeURIComponent(props.selectedPeer.id)}/notify`, {
    emails: emails,
    webhooks: notifyWebhooks.value,
    events: notifyEvents.value
  }, () => {
    notifyLoaded.value = true
  })
}

function toggleWebhook(id) {
  const idx = notifyWebhooks.value.indexOf(id)
  if (idx >= 0) notifyWebhooks.value.splice(idx, 1)
  else notifyWebhooks.value.push(id)
}

function toggleEvent(id) {
  const idx = notifyEvents.value.indexOf(id)
  if (idx >= 0) notifyEvents.value.splice(idx, 1)
  else notifyEvents.value.push(id)
}

loadNotifyConfig()
defineEmits(['close'])
</script>

<template>
	<div class="peerSettingContainer w-100 h-100 position-absolute top-0 start-0 overflow-y-scroll ">
		<div class="d-flex h-100 w-100 pb-2">
			<div class="m-auto w-100 p-2">
				<div class="card rounded-3 shadow h-100" >
					<div class="card-header bg-transparent d-flex align-items-center gap-2 border-0 p-4 pb-2">
						<h4 class="mb-0 fw-normal">
							<LocaleText t="Peer Details"></LocaleText>
						</h4>
						<button type="button" class="btn-close ms-auto" @click="$emit('close')" aria-label="Close"></button>
					</div>
					<div class="card-body px-4">
						<div>
							<p class="mb-0 text-muted"><small>
								<LocaleText t="Peer"></LocaleText>
							</small></p>
							<h2>
								{{ selectedPeer.name }}
							</h2>
						</div>
						<div class="row mt-3 gy-2 gx-2 mb-2">
							<div class="col-12 col-lg-3">
								<div class="card rounded-3 bg-transparent h-100">
									<div class="card-body py-2 d-flex flex-column justify-content-center">
										<p class="mb-0 text-muted"><small>
											<LocaleText t="Status"></LocaleText>
										</small></p>
										<div class="d-flex align-items-center">
											<span class="dot ms-0 me-2" :class="{active: selectedPeer.status === 'running'}"></span>
											<LocaleText t="Connected" v-if="selectedPeer.status === 'running'"></LocaleText>
											<LocaleText t="Disconnected" v-else></LocaleText>
										</div>
									</div>
								</div>
							</div>
							<div class="col-12 col-lg-3">
								<div class="card rounded-3 bg-transparent  h-100">
									<div class="card-body py-2 d-flex flex-column justify-content-center">
										<p class="mb-0 text-muted"><small>
											<LocaleText t="Allowed IPs"></LocaleText>
										</small></p>
										{{selectedPeer.allowed_ip}}
									</div>
								</div>
							</div>
							<div style="word-break: break-all" class="col-12 col-lg-6">
								<div class="card rounded-3 bg-transparent h-100">
									<div class="card-body py-2 d-flex flex-column justify-content-center">
										<p class="mb-0 text-muted"><small>
											<LocaleText t="Public Key"></LocaleText>
										</small></p>
										<samp>{{selectedPeer.id}}</samp>
									</div>
								</div>
							</div>
							<div class="col-12" v-if="selectedPeer.notes && selectedPeer.notes.length > 0">
								<div class="card rounded-3 bg-transparent h-100">
									<div class="card-body py-2">
										<p class="mb-1 text-muted"><small>
											<LocaleText t="Notes"></LocaleText>
										</small></p>
										<p class="mb-0" style="white-space: pre-wrap">{{selectedPeer.notes}}</p>
									</div>
								</div>
							</div>

							<div class="col-12 col-lg-3">
								<div class="card rounded-3 bg-transparent  h-100">
									<div class="card-body d-flex">
										<div>
											<p class="mb-0 text-muted"><small>
												<LocaleText t="Latest Handshake Time"></LocaleText>
											</small></p>
											<strong class="h4">
												<LocaleText :t="selectedPeer.latest_handshake !== 'No Handshake' ? selectedPeer.latest_handshake + ' ago': 'No Handshake'"></LocaleText>
											</strong>
										</div>
										<i class="bi bi-person-raised-hand ms-auto h2 text-muted"></i>
									</div>
								</div>
							</div>
							<div class="col-12 col-lg-3">
								<div class="card rounded-3 bg-transparent  h-100">
									<div class="card-body d-flex">
										<div>
											<p class="mb-0 text-muted"><small>
												<LocaleText t="Total Usage"></LocaleText>
											</small></p>
											<strong class="h4 text-warning">
												{{ (selectedPeer.total_data + selectedPeer.cumu_data).toFixed(4) }} GB
											</strong>
										</div>
										<i class="bi bi-arrow-down-up ms-auto h2 text-muted"></i>
									</div>
								</div>
							</div>
							<div class="col-12 col-lg-3">
								<div class="card rounded-3 bg-transparent  h-100">
									<div class="card-body d-flex">
										<div>
											<p class="mb-0 text-muted"><small>
												<LocaleText t="Total Received"></LocaleText>
											</small></p>
											<strong class="h4 text-primary">{{(selectedPeer.total_receive + selectedPeer.cumu_receive).toFixed(4)}} GB</strong>
										</div>
										<i class="bi bi-arrow-down ms-auto h2 text-muted"></i>
									</div>
								</div>
							</div>
							<div class="col-12 col-lg-3">
								<div class="card rounded-3 bg-transparent  h-100">
									<div class="card-body d-flex">
										<div>
											<p class="mb-0 text-muted"><small>
												<LocaleText t="Total Sent"></LocaleText>
											</small></p>
											<strong class="h4 text-success">{{(selectedPeer.total_sent + selectedPeer.cumu_sent).toFixed(4)}} GB</strong>
										</div>
										<i class="bi bi-arrow-up ms-auto h2 text-muted"></i>
									</div>
								</div>
							</div>
							<div class="col-12">
                                                                <div class="card rounded-3 bg-transparent">
                                                                        <div class="card-body">
                                                                                <div class="d-flex align-items-center mb-3">
                                                                                        <i class="bi bi-bell me-2"></i>
                                                                                        <h6 class="mb-0"><LocaleText t="Notification Settings"></LocaleText></h6>
                                                                                </div>
                                                                                <div class="row g-3" v-if="notifyLoaded">
                                                                                        <div class="col-12 col-md-6">
                                                                                                <label class="form-label small text-muted"><LocaleText t="Email Addresses"></LocaleText></label>
                                                                                                <input type="text" class="form-control form-control-sm bg-secondary bg-opacity-25 text-white"
                                                                                                        v-model="notifyEmails" placeholder="admin@example.com, tech@example.com">
                                                                                        </div>
                                                                                        <div class="col-12 col-md-6">
                                                                                                <label class="form-label small text-muted"><LocaleText t="Events"></LocaleText></label>
                                                                                                <div v-for="evt in availableEvents" :key="evt.id" class="form-check form-check-inline">
                                                                                                        <input class="form-check-input" type="checkbox" :id="'pd_evt_' + evt.id"
                                                                                                                :checked="notifyEvents.includes(evt.id)" @change="toggleEvent(evt.id)">
                                                                                                        <label class="form-check-label small" :for="'pd_evt_' + evt.id">
                                                                                                                <LocaleText :t="evt.label"></LocaleText>
                                                                                                        </label>
                                                                                                </div>
                                                                                        </div>
                                                                                        <div class="col-12" v-if="availableWebhooks.length > 0">
                                                                                                <label class="form-label small text-muted"><LocaleText t="Webhooks"></LocaleText></label>
                                                                                                <div v-for="wh in availableWebhooks" :key="wh.WebHookID" class="form-check form-check-inline">
                                                                                                        <input class="form-check-input" type="checkbox" :id="'pd_wh_' + wh.WebHookID"
                                                                                                                :checked="notifyWebhooks.includes(wh.WebHookID)" @change="toggleWebhook(wh.WebHookID)">
                                                                                                        <label class="form-check-label small" :for="'pd_wh_' + wh.WebHookID">{{ wh.PayloadURL }}</label>
                                                                                                </div>
                                                                                        </div>
                                                                                        <div class="col-12 text-end">
                                                                                                <button class="btn btn-sm btn-primary" @click="saveNotifyConfig">
                                                                                                        <i class="bi bi-check-lg me-1"></i><LocaleText t="Save"></LocaleText>
                                                                                                </button>
                                                                                        </div>
                                                                                </div>
                                                                        </div>
                                                                </div>
                                                        </div>
							<div class="col-12">
								<PeerTraffics
									:selectedDate="selectedDate"
									:selectedPeer="selectedPeer"></PeerTraffics>
							</div>
							<div class="col-12">
								<PeerSessions
									:selectedDate="selectedDate"
									@selectDate="args => selectedDate = args"
									:selectedPeer="selectedPeer"></PeerSessions>
							</div>
							<div class="col-12">
								<PeerEndpoints
									:selectedPeer="selectedPeer"
								>
								</PeerEndpoints>
							</div>
						</div>


					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<style scoped>

</style>
