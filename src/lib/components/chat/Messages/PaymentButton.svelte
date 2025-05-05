<script lang="ts">
	export let label = 'Charge Your Account.';
	export let webPaymentUrl = 'https://www.aibrary.dev/chat/payment?chat=true';
	let loading = false;

	async function initiatePayment() {
		try {
			loading = true;

			if ('getDigitalGoodsService' in window) {
  // Digital Goods API is supported!
  try {
    const service =
        await window.getDigitalGoodsService('https://play.google.com/billing');
    // Google Play Billing is supported!

  } catch (error) {
	window.location.href = webPaymentUrl;
    return;
  }
}

			// // Check if Digital Goods API is available
			// if (navigator.digitalGoods && navigator.digitalGoods.getService) {
			// 	console.log("Inside Digital Goods API check");

			// 	const digitalGoods = await navigator.digitalGoods.getService('play');

			// 	const sku = 'credit_5usd';

			// 	const paymentRequest = new PaymentRequest(
			// 		[
			// 			{
			// 				supportedMethods: 'https://play.google.com/billing',
			// 				data: { sku }
			// 			}
			// 		],
			// 		{
			// 			total: {
			// 				label: '5 USD Credit',
			// 				amount: { currency: 'USD', value: '5.35' }
			// 			}
			// 		}
			// 	);

			// 	const paymentResponse = await paymentRequest.show();
			// 	await paymentResponse.complete('success');

			// 	alert('Payment successful via Google Play! 🎉');
			// 	location.reload();
			// } else {
			// 	// Fallback for web users (redirect to manual payment page)
			// 	window.location.href = webPaymentUrl;
			// }
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
