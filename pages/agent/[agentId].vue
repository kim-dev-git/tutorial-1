<script setup>
  const { agentId } = useRoute().params

  const { data: agent, pending } = useLazyFetch(
    `https://valorant-api.com/v1/agents/${agentId}`,
    {
      onResponse({ response }) {
        const data = response._data?.data
        const abilities = data?.abilities
        selectedAbility.value = abilities?.at?.(0)
      },
      server: false,
    }
  )
  const selectedAbility = ref()
  function selectAbility(ability) {
    selectedAbility.value = ability
  }
</script>

<template>
  <v-theme-provider theme="dark" with-background>
    <v-img src="/bg.gif" cover>
      <div>
        <v-btn
          variant="text"
          prepend-icon="mdi-arrow-left"
          @click="navigateTo('/agent')"
        >
          Agent List
        </v-btn>
      </div>

      <h1>Agent - {{ agent?.data.displayName }}</h1>

      <v-row v-if="agent?.data">
        <v-col>
          <v-img :src="agent.data.background" />
        </v-col>

        <v-col>
          <div class="d-flex">
            <v-img :src="agent.data.fullPortraitV2" height="700" cover />
          </div>
        </v-col>

        <v-col class="px-8">
          <div class="d-flex flex-column">
            <div>{{ agent.data.role.displayName }}</div>
            <div class="mb-8 text-h2 font-weight-bold text-uppercase">
              {{ agent.data.displayName }}
            </div>
          </div>

          <v-row>
            <v-col cols="12">
              <v-row>
                <v-col cols="2">
                  <v-card class="pa-3" width="64" height="64">
                    <v-img
                      :src="agent.data.role.displayIcon"
                      width="64"
                      height="64"
                    />
                  </v-card>
                </v-col>
                <v-col>
                  <v-card variant="tonal" class="pa-4">
                    {{ agent.data.description }}
                  </v-card>
                </v-col>
              </v-row>
            </v-col>
            <v-col
              v-for="ability in agent.data.abilities.filter(
                (a) => a.slot != 'Passive'
              )"
              :key="ability.slot"
              cols="3"
            >
              <v-card class="pa-3" @click="selectAbility(ability)">
                <div class="text-center mb-3">{{ ability.slot }}</div>
                <v-img :src="ability.displayIcon" />
              </v-card>
            </v-col>
          </v-row>

          <v-card
            :title="selectedAbility?.displayName"
            :text="selectedAbility?.description"
            variant="tonal"
            class="mt-6"
          >
          </v-card>
        </v-col>
      </v-row>
    </v-img>
  </v-theme-provider>
</template>
