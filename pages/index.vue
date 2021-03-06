<template>
  <!-- eslint-disable vue/v-slot-style -->
  <div class="container">
    <CBox
      d="flex"
      w="100vw"
      h="100vh"
      flex-dir="column"
      justify-content="center"
    >
      <CFlex justify="center" direction="column" align="center">
        <FormulateForm v-model="form">
          <CStack :spacing="5">
            <FormulateInput
              type="email"
              name="email"
              label="Your email"
              validation="required|email"
            />
            <FormulateInput
              type="text"
              name="fullName"
              label="Your full name"
              validation="required"
              validation-name="Full Name"
            />
            <FormulateInput
              #default="{ index }"
              type="group"
              name="proposal"
              :repeatable="true"
              label="Proposal(s)"
              add-label="+ Add another proposal"
              validation="required"
            >
              <CollapsibleGroupItem
                :index="index"
                :title="getProposal(index, 'name')"
                default-title="Proposal"
              >
                <div class="proposal">
                  <FormulateInput
                    name="name"
                    validation="required"
                    label="Proposal name"
                  />
                  <FormulateInput
                    type="select"
                    name="fund"
                    label="Proposal in fund"
                    :options="fundsOption"
                  />

                  <FormulateInput
                    name="reportDetailsPublish"
                    :options="{
                      yes: 'Yes, make it public',
                      no: 'No, not at this time',
                    }"
                    type="radio"
                    label="Are your report details ok to be public?"
                  />

                  <CStack :spacing="5">
                    <CText font-size="md">
                      For proposal {{ getProposal(index, 'name') || '...' }}
                    </CText>

                    <CText font-size="md">
                      Describe two significant setbacks, obstacles, or failures
                      you had this month and what you learned from each
                      (regardless of wether or not you solved them). Your answer
                      will not be shared with the public. Text box will expand
                      as you type.*
                    </CText>

                    <FormulateInput
                      name="proposalObstacles"
                      type="textarea"
                      label="Your answer"
                      validation="required"
                    />

                    <FormulateInput
                      type="date"
                      name="launchDate"
                      label="Proposal launch date"
                      validation="required"
                      min="2018-12-01"
                    />

                    <FormulateInput
                      name="proposalLaunched"
                      :options="{
                        no: 'No',
                        yes: 'Yes',
                      }"
                      type="radio"
                      label="Has your proposal launched?"
                    />

                    <div
                      v-if="getProposal(index, 'proposalLaunched') === 'yes'"
                    >
                      <CText font-size="md">
                        What challenge was your proposal submitted in?
                      </CText>

                      <FormulateInput
                        name="challenge"
                        :options="challengesOption[getProposal(index, 'fund')]"
                        type="select"
                        placeholder="Select challenge"
                        label="List of challenges in selected fund"
                      />

                      <FormulateInput
                        name="succesFactor"
                        :options="
                          getSuccessFactors(getProposal(index, 'challenge'))
                        "
                        type="select"
                        placeholder="Select succes factor"
                        label="List of succes factors in selected fund"
                      />
                    </div>
                  </CStack>
                </div>
              </CollapsibleGroupItem>
            </FormulateInput>
          </CStack>
        </FormulateForm>
      </CFlex>
    </CBox>
    <pre>{{ form }}</pre>
  </div>
</template>

<script lang="js">
import {
  CBox,
  CFlex,
  CStack,
  CText,
} from '@chakra-ui/vue'

import CollapsibleGroupItem from '../components/CollapsibleGroupItem.vue';

export default {
  name: 'App',
  components: {
    CBox,
    CFlex,
    CStack,
    CText,
    CollapsibleGroupItem
  },
  async asyncData ({ $content }) {
    const { funds } = await $content('funds').fetch();
    const { challenges } = await $content('challenges').fetch();

    const fundsOption = funds.map(fund => ({
      label: fund,
      value: fund
    }))

    const mutatedChallenges = challenges.map(challenge => ({
      ...challenge,
      value: challenge.label,
      success_factors: challenge.success_factors.map(successFactor => ({
        label: successFactor,
        value: successFactor
      }))
    }));

    const challengesOption = {};
    fundsOption.forEach(fund => {
      challengesOption[fund.value] = mutatedChallenges.filter(({ fund: challengeFund, label, value }) => challengeFund === fund.label && { label, value });
    });

    return {
      fundsOption,
      challenges: mutatedChallenges,
      challengesOption
    }
  },
  data () {
    return {
      form: {}
    }
  },
  computed: {},
  methods: {
    getSuccessFactors(challengeName) {
      if (!challengeName) return [];
      return this.challenges.find(({ label }) => label === challengeName).success_factors;
    },
    getProposal (index, value) {
      const proposal = this.form.proposal && this.form.proposal.find((el, i) => i === index);
      if(value) {
        return proposal && proposal[value];
      }else{
        return proposal;
      }
    }
  }
}
</script>
