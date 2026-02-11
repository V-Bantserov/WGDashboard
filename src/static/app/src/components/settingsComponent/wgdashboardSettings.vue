<script setup>
import {DashboardConfigurationStore} from "@/stores/DashboardConfigurationStore.js";
import LocaleText from "@/components/text/localeText.vue";
import DashboardLanguage from "@/components/settingsComponent/dashboardLanguage.vue";
import AccountSettingsInputPassword from "@/components/settingsComponent/accountSettingsInputPassword.vue";
import DashboardTheme from "@/components/settingsComponent/dashboardTheme.vue";
import DashboardAPIKeys from "@/components/settingsComponent/dashboardAPIKeys.vue";
import DashboardIPPortInput from "@/components/settingsComponent/dashboardIPPortInput.vue";
import AccountSettingsMFA from "@/components/settingsComponent/accountSettingsMFA.vue";
import AccountSettingsInputUsername from "@/components/settingsComponent/accountSettingsInputUsername.vue";
import DashboardEmailSettings from "@/components/settingsComponent/dashboardEmailSettings.vue";
import DashboardWebHooks from "@/components/settingsComponent/dashboardWebHooks.vue";
import DashboardAdminUsers from "@/components/settingsComponent/dashboardAdminUsers.vue";
import {fetchPost} from "@/utilities/fetch.js";

const dashboardConfigurationStore = DashboardConfigurationStore()

const updateMenuSetting = async (key, event) => {
        const value = event.target.checked ? 'true' : 'false';
        await fetchPost("/api/updateDashboardConfigurationItem", {
                section: "Server",
                key: key,
                value: value
        }, (res) => {
                if (res.status){
                        dashboardConfigurationStore.Configuration.Server[key] = value;
                }
        });
}
</script>
<template>
        <div class="d-flex gap-3 flex-column">
                <div class="card rounded-3">
                        <div class="card-header">
                                <h6 class="my-2">
                                        <i class="bi bi-magic me-2"></i>
                                        <LocaleText t="Appearance"></LocaleText>
                                </h6>
                        </div>
                        <div class="card-body">
                                <div class="row g-2">
                                        <div class="col-sm">
                                                <DashboardTheme></DashboardTheme>
                                        </div>
                                        <div class="col-sm">
                                                <DashboardLanguage></DashboardLanguage>
                                        </div>
                                </div>
                        </div>
                </div>
                <div class="card rounded-3">
                        <div class="card-header">
                                <h6 class="my-2">
                                        <i class="bi bi-list me-2"></i>
                                        <LocaleText t="Menu Visibility"></LocaleText>
                                </h6>
                        </div>
                        <div class="card-body">
                                <div class="row g-3">
                                        <div class="col-sm-4">
                                                <div class="form-check form-switch">
                                                        <input class="form-check-input" type="checkbox" role="switch" 
                                                                id="menuClients"
                                                                :checked="String(dashboardConfigurationStore.Configuration.Server.menu_clients) === 'true'"
                                                                @change="updateMenuSetting('menu_clients', $event)">
                                                        <label class="form-check-label" for="menuClients">
                                                                <LocaleText t="Show Clients"></LocaleText>
                                                        </label>
                                                </div>
                                        </div>
                                        <div class="col-sm-4">
                                                <div class="form-check form-switch">
                                                        <input class="form-check-input" type="checkbox" role="switch" 
                                                                id="menuWebhooks"
                                                                :checked="String(dashboardConfigurationStore.Configuration.Server.menu_webhooks) === 'true'"
                                                                @change="updateMenuSetting('menu_webhooks', $event)">
                                                        <label class="form-check-label" for="menuWebhooks">
                                                                <LocaleText t="Show Webhooks"></LocaleText>
                                                        </label>
                                                </div>
                                        </div>
                                        <div class="col-sm-4">
                                                <div class="form-check form-switch">
                                                        <input class="form-check-input" type="checkbox" role="switch" 
                                                                id="menuHelp"
                                                                :checked="String(dashboardConfigurationStore.Configuration.Server.menu_help) === 'true'"
                                                                @change="updateMenuSetting('menu_help', $event)">
                                                        <label class="form-check-label" for="menuHelp">
                                                                <LocaleText t="Show Help"></LocaleText>
                                                        </label>
                                                </div>
                                        </div>
                                        <div class="col-sm-4">
                                                <div class="form-check form-switch">
                                                        <input class="form-check-input" type="checkbox" role="switch"
                                                                id="menuSystemStatus"
                                                                :checked="String(dashboardConfigurationStore.Configuration.Server.menu_system_status) === 'true'"
                                                                @change="updateMenuSetting('menu_system_status', $event)">
                                                        <label class="form-check-label" for="menuSystemStatus">
                                                                <LocaleText t="Show System Status"></LocaleText>
                                                        </label>
                                                </div>
                                        </div>
                                        <div class="col-sm-4">
                                                <div class="form-check form-switch">
                                                        <input class="form-check-input" type="checkbox" role="switch"
                                                                id="menuPing"
                                                                :checked="String(dashboardConfigurationStore.Configuration.Server.menu_ping) === 'true'"
                                                                @change="updateMenuSetting('menu_ping', $event)">
                                                        <label class="form-check-label" for="menuPing">
                                                                <LocaleText t="Show Ping"></LocaleText>
                                                        </label>
                                                </div>
                                        </div>
                                        <div class="col-sm-4">
                                                <div class="form-check form-switch">
                                                        <input class="form-check-input" type="checkbox" role="switch"
                                                                id="menuTraceroute"
                                                                :checked="String(dashboardConfigurationStore.Configuration.Server.menu_traceroute) === 'true'"
                                                                @change="updateMenuSetting('menu_traceroute', $event)">
                                                        <label class="form-check-label" for="menuTraceroute">
                                                                <LocaleText t="Show Traceroute"></LocaleText>
                                                        </label>
                                                </div>
                                        </div>
                                        <div class="col-sm-4">
                                                <div class="form-check form-switch">
                                                        <input class="form-check-input" type="checkbox" role="switch"
                                                                id="menuHealthMonitor"
                                                                :checked="String(dashboardConfigurationStore.Configuration.Server.menu_health_monitor) === 'true'"
                                                                @change="updateMenuSetting('menu_health_monitor', $event)">
                                                        <label class="form-check-label" for="menuHealthMonitor">
                                                                <LocaleText t="Show Health Monitor"></LocaleText>
                                                        </label>
                                                </div>
                                        </div>
                                </div>
                        </div>
                </div>
                <div class="card rounded-3">
                        <div class="card-header">
                                <h6 class="my-2">
                                        <i class="bi bi-ethernet me-2"></i>
                                        <LocaleText t="Dashboard IP Address & Listen Port"></LocaleText>
                                </h6>
                        </div>
                        <div class="card-body">
                                <DashboardIPPortInput></DashboardIPPortInput>
                        </div>
                </div>
                <DashboardAPIKeys></DashboardAPIKeys>
                <DashboardEmailSettings></DashboardEmailSettings>
        </div>
</template>
<style scoped>
</style>