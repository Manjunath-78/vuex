<template>
  <div>
    <SnackBar :SnackBarComponent="SnackBarComponent" />
    <v-card
      variant="outlined"
      class="ma-5 mb-10"
      :class="$vuetify.display.xs ? 'amountdet pa-2' : 'mt-4 mx-8 mb-15 pa-4'"
    >
      <v-row class="mb-4">
        <v-btn
          v-if="
            menthumCore.init_from === 'Corporate' ||
            menthumCore.init_from === 'Corporate Mobile'
          "
          variant="flat"
          class="text-capitalize text-primary"
          :class="$vuetify.display.xs ? 'ml-2 mt-2' : 'ml-3 mt-3'"
          @click="txnWorkFlowAction"
        >
          <v-icon
            :start="!$vuetify.locale.isRtl"
            :end="$vuetify.locale.isRtl"
            size="small"
            >mdi-format-list-bulleted</v-icon
          >
          <span class="ml-2">{{ $t("WorkFlowList") }}</span>
        </v-btn>
        <v-spacer />
        <v-btn
          variant="flat"
          color="primary"
          class="text-capitalize"
          :class="
            $vuetify.display.xs || $vuetify.locale.isRtl ? 'mr-4' : 'ml-4'
          "
          @click="menthumcoreDetailsEmit(1)"
        >
          <v-icon
            :start="!$vuetify.locale.isRtl"
            :end="$vuetify.locale.isRtl"
            size="20"
            >mdi-arrow-left-bold-circle</v-icon
          >
          <span class="ml-2">{{ $t("Back") }}</span>
        </v-btn>
      </v-row>
      <v-row>
        <v-col cols="12" sm="8" md="8">
          <v-form ref="form">
            <v-row>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("TransactionInternalID") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{ menthumCore.txn_id_internal || "-" }}
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("TransactionExternalID") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{ paymentDetailsObj.txn_id_external || "-" }}
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("status") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{ menthumCore.status || "-" }}
              </v-col>
              <v-col
                v-if="methumcore_obj.action !== 'CREATE'"
                cols="12"
                sm="6"
                class="font-weight-bold text-body-1"
              >
                {{ $t("type") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                <span
                  v-if="menthumCore.payment_type === 'Credit'"
                  class="text-green"
                >
                  {{ menthumCore.payment_type }}
                </span>
                <span
                  v-else-if="menthumCore.payment_type === 'Debit'"
                  class="text-red"
                >
                  {{ menthumCore.payment_type }}
                </span>
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("InitiatedBy") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{ menthumCore.initiated_by || "-" }}
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("ApprovedBy") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{ menthumCore.approved_by || "-" }}
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("CreditInitiatedOn") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{ menthumCore.credit_initiated_on || "-" }}
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("TxnInitiatedDate&Time") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{ menthumCore.created_on || "-" }}
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("TxnExecutedDate&Time") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{ menthumCore.updated_on || "-" }}
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("Source") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                <div v-if="SourceName" class="font-weight-bold">
                  <span>{{ SourceName }}</span
                  ><br />
                  <span>{{ SourceBankName }}</span
                  ><br />
                  <span>{{ SourceBankAccName }}</span>
                </div>
                <div v-else class="text-caption ml-2">NA</div>
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("destination") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                <div v-if="DestinationName" class="font-weight-bold">
                  <span>{{ DestinationName }}</span
                  ><br />
                  <span>{{ DestinationBankName }}</span
                  ><br />
                  <span>{{ DestinationBankAccName }}</span>
                </div>
                <div v-else class="text-caption ml-2">NA</div>
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("mode") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{ paymentDetailsObj.mode || "-" }}
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("route") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                <span v-if="menthumCore.route_id === '1'">{{ $t("ACH") }}</span>
                <span v-else-if="menthumCore.route_id === '2'">{{
                  $t("IPN")
                }}</span>
                <span v-else-if="menthumCore.route_id === '3'">{{
                  $t("Internal")
                }}</span>
                <span v-else-if="menthumCore.route_id === '4'">{{
                  $t("External")
                }}</span>
                <span v-else>-</span>
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("GrossAmount") }} (USD):
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{
                  menthumCore.gross_amt ? commafy(menthumCore.gross_amt) : "-"
                }}
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("NetAmount") }} (USD):
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{ menthumCore.amount ? commafy(menthumCore.amount) : "-" }}
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("usdresidual") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{
                  menthumCore.usd_residual
                    ? commafy(menthumCore.usd_residual)
                    : "-"
                }}
              </v-col>
              <v-col cols="12" sm="6" class="font-weight-bold text-body-1">
                {{ $t("ProcessingFee") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{ menthumCore.txn_fee ? commafy(menthumCore.txn_fee) : "-" }}
              </v-col>
              <v-col
                v-if="methumcore_obj.action !== 'CREATE'"
                cols="12"
                sm="6"
                class="font-weight-bold text-body-1"
              >
                {{ $t("TotalBalance") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{
                  menthumCore.closing_balance
                    ? commafy(menthumCore.closing_balance)
                    : "-"
                }}
              </v-col>
              <v-col
                v-if="methumcore_obj.action !== 'CREATE'"
                cols="12"
                sm="6"
                class="font-weight-bold text-body-1"
              >
                {{ $t("ClearedFunds") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{
                  menthumCore.cleared_fund
                    ? commafy(menthumCore.cleared_fund)
                    : "-"
                }}
              </v-col>
              <v-col
                v-if="methumcore_obj.action !== 'CREATE'"
                cols="12"
                sm="6"
                class="font-weight-bold text-body-1"
              >
                {{ $t("UnclearedFunds") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{
                  menthumCore.not_cleared_fund
                    ? commafy(menthumCore.not_cleared_fund)
                    : "-"
                }}
              </v-col>
              <v-col
                v-if="methumcore_obj.action !== 'CREATE'"
                cols="12"
                sm="6"
                class="font-weight-bold text-body-1"
              >
                {{ $t("Remarks") }}:
              </v-col>
              <v-col cols="12" sm="6" class="text-body-1">
                {{ menthumCore.remark || "-" }}
              </v-col>
            </v-row>
          </v-form>
        </v-col>
        <v-col cols="12" sm="4" md="4">
          <v-card variant="flat" class="mt-8">
            <v-img
              :src="menthumCore.receipt_image_url || ''"
              aspect-ratio="1.7"
              contain
            />
          </v-card>
        </v-col>
      </v-row>
      
      <!-- Fixed WorkflowDialog component -->
      <WorkflowDialog
        v-model="TxnWorkFlowDialog"
        :menthumCore="menthumCore"
        @update:modelValue="txnWorkFlowDialogEmit"
      />
    </v-card>
  </div>
</template>

<script>
import { format, parseISO } from "date-fns";
import WorkflowDialog from "./WorkflowDialog.vue";
import SnackBar from "@/components/Extras/SnackBar.vue";
import { commonAPICallMethod } from "@/mixins/commonAPICallMethod.js";

export default {
  name: "TransactionDetails",
  components: {
    SnackBar,
    WorkflowDialog,
  },
  mixins: [commonAPICallMethod],
  props: {
    methumcore_obj: {
      type: Object,
      required: true,
    },
    menthumCoreListItems: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      SourceName: "",
      SourceBankName: "",
      SourceBankAccName: "",
      DestinationName: "",
      DestinationBankName: "",
      DestinationBankAccName: "",
      TxnWorkFlowDialog: false,
      SnackBarComponent: {},
      MenthumcoreDetailsById: {},
      menthumCore: {
        payment_id: "",
        source_payment: "",
        customer_name: "",
        email_id: "",
        phone_number: "",
        created_on: format(parseISO(new Date().toISOString()), "yyyy-MM-dd"),
        updated_on: format(parseISO(new Date().toISOString()), "yyyy-MM-dd"),
        time: "",
        route_id: "",
        status: "",
        amount: "",
        txn_fee: "",
        payment_type: "",
        txn_id_internal: "",
        initiated_by: "",
        approved_by: "",
        credit_initiated_on: "",
        closing_balance: "",
        cleared_fund: "",
        not_cleared_fund: "",
        remark: "",
        receipt_image_url: "",
        init_from: "Corporate",
      },
      paymentDetailsObj: {
        acc_no: "",
        bank_id: "",
        bank_name: "",
        destination: "",
        mode: "",
        txn_id_external: "",
        creditor_name: "",
        withdrawer_name: "",
        source_user_name: "",
        dest_user_name: "",
        source_name: "",
        cheque_no: "",
        cheque_number: "",
      },
    };
  },
  mounted() {
    if (this.methumcore_obj.action === "EDIT") {
      this.getMenthumByID();
    }
  },
  methods: {
    async getMenthumByID() {
      const param = {
        payment_id: this.methumcore_obj.payment_id,
        module_code: "CTH",
      };
      this.overlay = true;
      try {
        const res = await this.commonQueryMethod(
          "PaymentDetailsByIdUSD",
          param
        );
        if (res?.status === "00") {
          this.MenthumcoreDetailsById = res.data;
          this.menthumCore = { ...this.MenthumcoreDetailsById };
          this.paymentDetailsObj = { ...this.menthumCore.payment_details };
          this.getSourceNameDetails(this.menthumCore);
          this.getSourceBankNameDetails(this.menthumCore);
          this.getSourceBankAccNameDetails(this.menthumCore);
          this.getDestinationNameDetails(this.menthumCore);
          this.getDestinationBankNameDetails(this.menthumCore);
          this.getDestinationBankAccNameDetails(this.menthumCore);
        } else {
          this.MenthumcoreDetailsById = {};
          this.SnackBarComponent = {
            SnackbarVmodel: true,
            SnackbarColor: "red",
            SnackbarText: res?.status_message || "No data available",
          };
        }
      } catch (error) {
        this.SnackBarComponent = {
          SnackbarVmodel: true,
          SnackbarColor: "red",
          SnackbarText: "Error fetching transaction details",
        };
      } finally {
        this.overlay = false;
      }
    },
    commafy(num) {
      if (num != null && num !== undefined && num !== "") {
        const str = num.toString().split(".");
        if (str[0].length >= 4) {
          str[0] = str[0].replace(/(\d)(?=(\d{3})+$)/g, "$1,");
        }
        if (str[1] && str[1].length >= 4) {
          str[1] = str[1].replace(/(\d{3})/g, "$1");
        }
        return str.join(".");
      }
      return num || "-";
    },
    getSourceNameDetails(item) {
      this.paymentDetailsObj = item.payment_details || this.paymentDetailsObj;
      if (!item) return "";
      if (item.payment_type === "Credit") {
        if (
          [
            "Bank Transfer",
            "Wire Transfer",
            "Cheque",
            "Cash",
            "Net Banking",
          ].includes(this.paymentDetailsObj.mode)
        ) {
          this.SourceName =
            this.paymentDetailsObj.creditor_name || item.customer_name || "";
        } else if (
          ["Internal Transfer", "Internal Transfer(P2P)", "P2P"].includes(
            this.paymentDetailsObj.mode
          )
        ) {
          this.SourceName =
            (this.paymentDetailsObj.source_user_name ||
              this.paymentDetailsObj.creditor_name ||
              item.customer_name ||
              "") + " Menthum A/c";
        } else if (
          ["System", "Redemption Rewards", "Saving Reward"].includes(
            this.paymentDetailsObj.mode
          )
        ) {
          this.SourceName = this.paymentDetailsObj.source_name || "";
        }
      } else if (item.payment_type === "Debit") {
        if (
          [
            "Bank Transfer",
            "Wire Transfer",
            "Cheque",
            "Cash",
            "Net Banking",
            "ACH",
          ].includes(this.paymentDetailsObj.mode)
        ) {
          this.SourceName = ["Admin", "Mobile"].includes(item.init_from)
            ? (this.paymentDetailsObj.withdrawer_name ||
                this.paymentDetailsObj.creditor_name ||
                item.customer_name ||
                "") + " Menthum A/c"
            : this.paymentDetailsObj.withdrawer_name ||
              this.paymentDetailsObj.creditor_name ||
              item.customer_name ||
              "";
        } else if (
          this.paymentDetailsObj.mode === "" &&
          item.init_from === "Mobile"
        ) {
          this.SourceName =
            (this.paymentDetailsObj.withdrawer_name ||
              this.paymentDetailsObj.creditor_name ||
              item.customer_name ||
              "") + " Menthum A/c";
        } else if (
          ["Internal Transfer", "Internal Transfer(P2P)", "P2P"].includes(
            this.paymentDetailsObj.mode
          )
        ) {
          this.SourceName =
            (item.customer_name || this.paymentDetailsObj.creditor_name || "") +
            " Menthum A/c";
        } else if (
          ["System", "Withdrawal Fee"].includes(this.paymentDetailsObj.mode)
        ) {
          this.SourceName = (item.customer_name || "") + " Menthum A/c";
        }
      }
      return this.SourceName;
    },
    getSourceBankNameDetails(item) {
      this.paymentDetailsObj = item.payment_details || this.paymentDetailsObj;
      if (
        item &&
        item.payment_type === "Credit" &&
        [
          "Bank Transfer",
          "Wire Transfer",
          "Cheque",
          "Net Banking",
          "Internal Transfer",
          "Internal Transfer(P2P)",
          "P2P",
        ].includes(this.paymentDetailsObj.mode)
      ) {
        this.SourceBankName = this.paymentDetailsObj.bank_name || "";
      } else {
        this.SourceBankName = "";
      }
      return this.SourceBankName;
    },
    getSourceBankAccNameDetails(item) {
      this.paymentDetailsObj = item.payment_details || this.paymentDetailsObj;
      if (
        item &&
        item.payment_type === "Credit" &&
        [
          "Bank Transfer",
          "Wire Transfer",
          "Cheque",
          "Net Banking",
          "Internal Transfer",
          "Internal Transfer(P2P)",
          "P2P",
        ].includes(this.paymentDetailsObj.mode)
      ) {
        this.SourceBankAccName =
          this.paymentDetailsObj.acc_no ||
          this.paymentDetailsObj.cheque_no ||
          this.paymentDetailsObj.cheque_number ||
          this.paymentDetailsObj.txn_id_external ||
          "";
      } else {
        this.SourceBankAccName = "";
      }
      return this.SourceBankAccName;
    },
    getDestinationNameDetails(item) {
      this.paymentDetailsObj = item.payment_details || this.paymentDetailsObj;
      if (!item) return "";
      if (item.payment_type === "Credit") {
        if (
          [
            "Bank Transfer",
            "Wire Transfer",
            "Cheque",
            "Cash",
            "Net Banking",
          ].includes(this.paymentDetailsObj.mode)
        ) {
          this.DestinationName =
            (item.customer_name || this.paymentDetailsObj.destination || "") +
            " Menthum A/c";
        } else if (
          ["Internal Transfer", "Internal Transfer(P2P)", "P2P"].includes(
            this.paymentDetailsObj.mode
          )
        ) {
          this.DestinationName =
            (this.paymentDetailsObj.dest_user_name ||
              this.paymentDetailsObj.destination ||
              item.customer_name ||
              "") + " Menthum A/c";
        } else if (
          ["System", "Redemption Rewards", "Saving Reward"].includes(
            this.paymentDetailsObj.mode
          )
        ) {
          this.DestinationName = (item.customer_name || "") + " Menthum A/c";
        }
      } else if (item.payment_type === "Debit") {
        if (
          [
            "Bank Transfer",
            "Wire Transfer",
            "Cheque",
            "Cash",
            "Net Banking",
            "ACH",
          ].includes(this.paymentDetailsObj.mode)
        ) {
          this.DestinationName = ["Corporate", "Corporate Mobile"].includes(
            item.init_from
          )
            ? this.paymentDetailsObj.destination || ""
            : this.paymentDetailsObj.withdrawer_name ||
              this.paymentDetailsObj.creditor_name ||
              item.customer_name ||
              "";
        } else if (
          this.paymentDetailsObj.mode === "" &&
          item.init_from === "Mobile"
        ) {
          this.DestinationName =
            this.paymentDetailsObj.withdrawer_name ||
            this.paymentDetailsObj.creditor_name ||
            item.customer_name ||
            "";
        } else if (
          ["Internal Transfer", "Internal Transfer(P2P)", "P2P"].includes(
            this.paymentDetailsObj.mode
          )
        ) {
          this.DestinationName =
            (this.paymentDetailsObj.dest_user_name ||
              this.paymentDetailsObj.destination ||
              "") + " Menthum A/c";
        } else if (
          ["System", "Withdrawal Fee"].includes(this.paymentDetailsObj.mode)
        ) {
          this.DestinationName =
            this.paymentDetailsObj.destination ||
            (item.customer_name || "") + " Menthum A/c";
        }
      }
      return this.DestinationName;
    },
    getDestinationBankNameDetails(item) {
      this.paymentDetailsObj = item.payment_details || this.paymentDetailsObj;
      if (
        item &&
        item.payment_type === "Debit" &&
        [
          "Bank Transfer",
          "Wire Transfer",
          "Cheque",
          "Net Banking",
          "Internal Transfer",
          "Internal Transfer(P2P)",
          "P2P",
          "ACH",
          "",
        ].includes(this.paymentDetailsObj.mode)
      ) {
        this.DestinationBankName = this.paymentDetailsObj.bank_name || "";
      } else {
        this.DestinationBankName = "";
      }
      return this.DestinationBankName;
    },
    getDestinationBankAccNameDetails(item) {
      this.paymentDetailsObj = item.payment_details || this.paymentDetailsObj;
      if (
        item &&
        item.payment_type === "Debit" &&
        [
          "Bank Transfer",
          "Wire Transfer",
          "Cheque",
          "Net Banking",
          "Internal Transfer",
          "Internal Transfer(P2P)",
          "P2P",
          "ACH",
          "",
        ].includes(this.paymentDetailsObj.mode)
      ) {
        this.DestinationBankAccName =
          this.paymentDetailsObj.acc_no ||
          this.paymentDetailsObj.cheque_no ||
          this.paymentDetailsObj.cheque_number ||
          this.paymentDetailsObj.txn_id_external ||
          "";
      } else {
        this.DestinationBankAccName = "";
      }
      return this.DestinationBankAccName;
    },
    menthumcoreDetailsEmit(toggle) {
      this.$refs.form.reset();
      this.$emit("clicked", toggle);
    },
    // Fixed method - added console.log for debugging
    txnWorkFlowAction() {
      console.log("Button clicked - opening dialog");
      this.TxnWorkFlowDialog = true;
      console.log("TxnWorkFlowDialog set to:", this.TxnWorkFlowDialog);
    },
    // Fixed method to handle dialog closing
    txnWorkFlowDialogEmit(value) {
      console.log("Dialog emit received:", value);
      this.TxnWorkFlowDialog = value;
    },
  },
};
</script>

<style scoped>
.amountdet {
  font-size: 0.75rem;
  /* 11px equivalent */
  width: 350px;
}

.text-body-1 {
  font-size: 1rem;
  /* Matches Vuetify 3 text-body-1 */
}
</style>