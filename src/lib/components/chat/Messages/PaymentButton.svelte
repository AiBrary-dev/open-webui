<script lang="ts">
	export let label = 'Charge Your Account.';
	export let webPaymentUrl = 'https://www.aibrary.dev/chat/payment?chat=true';
	let loading = false;

	async function initiatePayment() {
		try {
			loading = true;

			if ('getDigitalGoodsService' in window) {

				// Fetch SKU details
				const service = await window.getDigitalGoodsService('https://play.google.com/billing');
				console.log(service);

				const skuDetails = await service.getDetails(['credit_5usd']);
				console.log(skuDetails);

				const item = skuDetails[0];

				// Format localized price (optional, for logging or showing in UI)
				const localizedPrice = new Intl.NumberFormat(
					navigator.language,
					{ style: 'currency', currency: item.price.currency }
				).format(item.price.value);
				console.log(`Localized Price: ${localizedPrice}`);

				// Define payment methods
				const paymentMethods = [{
					supportedMethods: 'https://play.google.com/billing',
					data: { sku: item.itemId }
				}];

				console.log(paymentMethods);

				

				// Define payment details and fill in from SKU
				// const paymentDetails = {
				// 	total: {
				// 		label: 'Total',
				// 		amount: {
				// 			currency: item.price.currency,
				// 			value: item.price.value
				// 		}
				// 	}
				// };
				const paymentDetails = {
					total: {
						label: `Total`,
						amount: {currency: `USD`, value: `0`}
					}
				};

				const request = new PaymentRequest(paymentMethods, paymentDetails);
				console.log(request);
				

				const paymentResponse = await request.show();

				const { purchaseToken, purchaseTime, orderId } = paymentResponse.details;
				console.log('Prod ID:',  item.itemId);
				console.log('Purchase Token:', purchaseToken);
				console.log('Purchase Time:', purchaseTime);
				console.log('Order ID:', orderId);
				// await paymentResponse.complete('success');


				// alert('Payment successful via Google Play! 🎉');
				location.reload();
			} else {
				// Fallback
				window.location.href = webPaymentUrl;
			}
		} catch (err) {
			console.error('Payment failed:', err);
			alert('Payment failed. Please try again.');
		} finally {
			loading = false;
		}
	}
</script>

<button
	on:click={initiatePayment}
	class="text-blue-600 dark:text-blue-400 underline hover:text-blue-800 dark:hover:text-blue-500 text-sm text-left disabled:opacity-50"
	disabled={loading}
>
	{#if loading}
		Loading...
	{:else}
		{label}
	{/if}
</button>
